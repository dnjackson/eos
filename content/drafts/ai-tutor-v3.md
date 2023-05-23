---
author: "Daniel Jackson"
title: "Notes on design of a GPT-powered tutor"
date: "2023-05-22"
description: "Notes on design of a GPT-powered tutor"
ShowToc: true
TocOpen: true
hideMeta: false # removes date etc from post
summary: "Notes on design of a GPT-powered tutor"
editPost:
  URL: "https://forum.softwareconcepts.io"
  Text: "Comments" # edit text
  appendFilePath: false # to append file path to Edit link
---

## Modifications

Changes since last version include: simplifying *Drill* so that student work is flat and just comprises answers to exercise parts, with the organization into exercises being only presentational within the *Exercise* concept; introducing the notion of a data concept, a concept that has routine CRUD actions and need only include name, purpose and state (and no OP or actions).

## Goals and Principles

These notes are intended to contribute to the design of a tutor based on GPT that Geoffrey is building for use in Software Studio this fall.

Some of the goals of building and deploying the tutor are:

- To get some initial experience using GPT-like tools in education;
- To help students learn concept design;
- To gather data both about students’ learning of concept design and about the performance of the AI tutor;
- To refine the pedagogy around concept design.

Some design principles of the tutor are:

- To make the simplest thing that works;
- While serving the needs of concept design, to make the tutor as general as possible so that it can be applied in other domains;
- To reduce barrier to usage by students;
- To make it easy for class staff to change the materials and exercises;
- To implement using the class tech stack--Node/Express, Vue (or React if that’s easier for Geoffrey)--so the tutor can be used as an exemplar for students and also easily modified by staff;

## Identifying concepts

The central concept captures the protocol of interacting with tools like GPT:

	concept ChatBot
	purpose interact with AI agent
	principle
	  prompt (t0); ask (t1); respond (t2); ...
	state
	  prompt: lone Text
	  questions, answers: seq Text
	actions
	  prompt (t: Text)
	    // initialize the chatbot with a prompt
	  ask (t: Text)
	    // user enters text
	  respond (t: Text)
	    // bot responds

The prompt will turn out to be a concatenation of distinct components, such as a general tactic for the bot to follow; a pedagogical context for the exercise at hand; and the starting seed for the exercise itself (eg, “ask the user to do X”).

The student will work in a mutable buffer, generating a sequence of immutable texts:

	concept Buffer
	purpose create immutable text with mutable buffer
	principle
	  // if you open buffer and edit it,
	  // saving produces last content
	  open (t); edit (t'); save (ts) {ts = t'}
	state
	  content: Text
	actions
	  open (t: Text)
	    // reset buffer contents to t
	  edit (t: Text)
	    // edit buffer content to be t
	  save (out t: Text)
	    // save contents of buffer
	  	
The *save* action is intended to be sync’d with another concept; this factors out the means by which the text is saved and where it’s saved to.

**Concept design theory aside**. This concept might seem so simple that its functionality should be absorbed into another concept (eg, *Drill*, below). But this would be a mistake. First, it embodies distinct and complex functionality--a complete text editor. The *edit* action represents very abstractly and generally all possible edits. Second, by separating the concept out we’re able to lift it (that is, create an indexed collection of instances without having to pollute any concept with a spurious index type).

The chatbot is fueled by a repository of advice, which I’ll model as a concept that offers advice on various topics:

	data concept Advice [Topic]
	purpose manage repository of advice indexed by topic
	state
	  advices: Topic -> set Text
	  name: Topic lone -> one Text
	actions
	  getAdvice (t: Topic, out advices: set Text)
    

**Concept design theory aside**. Using an experimental notion of a “data concept” that offers only simple CRUD actions. For now, the action signatures are given and the specs implied.

The pedagogical content is organized as a collection of exercises with multiple parts; the parts are associated with competencies (which will be the topics for advice).

	concept Exercise
	purpose maintain and offer structured exercises
	principle
	  // admin creates exercise with parts
	  // when student selects part, they get 
	  // the relevant descriptions
	  newExercise(te, e); newPart (e, tp, c, p); getPart (e, p, t', c')
	    {t' = te^tp, c' = c}
	state
	  parts: Exercise one -> set Part
	  competency: Part -> one Competency
	  description: Exercise + Part -> one Text
	actions
	  newCompetency (out c: Competency)
	  newExercise (t: Text, out e: Exercise)
	  newPart (e: Exercise, t: Text, c: Competency, out p: Part)
	  getExercise (e: Exercise, out t: Text)
	  getPart (e: Exercise, p: Part, out t: Text, out c: Competency)
	    // when student selects a part of an exercise,
	    // they're given a description that combines
	    // the description of the exercise with the 
	    // description of the part (and the competency
	    // they'll acquire)

**Notes**:
- Both exercises and their parts have descriptions
- Competency associated only with parts and not whole exercises. This will mean we can’t associate advice with exercises as a whole. Perhaps advice common to all parts is just included for those parts?
- Parts are not shared between exercises
- Considered including student behavior in this concept to make it richer but then decided it was more important to factor it out so that the student behavior concept could be lifted over students (that is, each student behavior being independent)
- *selectexercise* was added so that it could be synchronized with the resetting of the buffer as the student begins a new exercise.
- Competencies will presumably have textual names, but treating them abstractly in the concept.

Concept for student behavior:

	concept Drill [Task]
	purpose
	  store incremental student work on structured task
	principle
	  // if you select a task and one of its subtasks
	  // and then submit a solution, it's recorded
	  // appropriately
	  selectTask (t); selectSubTask (t, t'); 
	  submit (s) {t' in t.subtasks and t.solution = s}
	state
	  solution: Task -> lone Text
	  current: lone Task
	actions
	  selectTask (t: Task)
	    // set current to t
	  submit (t: Text)
	    // current.solution := t

**Notes**:
- Decided to make this cursor-based; not sure this is a good idea, but seems useful to have this state somewhere in the app.
- OP is not very satisfying but at least suggests how concept is used.

**An important design issue**. For simplicity, this design assumes that student work is saved only for subtasks (which will correspond to parts of exercises). There is no separate storage of whole exercise solutions. Instead, the text of an exercise solution is derived from the texts of the solutions of the parts. The student may be shown a coherent block of text representing their solution to the whole exercise but will not be able to edit it: only edits to solutions to parts are allowed. The *Drill* concept maintains the fragmentary solutions; the app can display a solution to an entire exercise by combining state from *Exercise* and *Drill*.

A concept is needed to manage student sessions and provide access to students to the work they have done:

	concept Session
	purpose maintain persistent identities of clients
	principle
	  // if you open a session on a client and then
	  // get the session at the client before closing,
	  // you'll get the session that was created on open
	  open (c, s); getSession (c, s') {s' = s}
	state
	  session: Client -> one Session
	actions
	  open (c: Client, out s: Session)
	    // create new session on client
	  getSession (c: Client, out s: Session)
	    // return session associated with client
	  close (s: Session)
	    // close session and forget client

**Note**. *Client* abstracts the implementation details of how the server associates session state with the user’s browser, tab etc. Probably easiest to generate a session id on *open* and install a cookie containing it.

A concept that contains all the functionality for remember email addresses and sending emails:

	concept Email [Principal]
	purpose support sending progress emails to users
	principle
	  // if you register a principal with an email
	  // and send an email to that principal
	  // then it will be sent to their address
	  register (p, a); send (p, t)
	    {message {t} sent to p.address}
	state
	  address: Principal -> one Address
	actions
	  register (p: Principal, a: Address)
	  forget (p: Principal)
	  send (p: Principal, t: Text)
	    // send a message containing t to p

## Composing concepts

All the concepts are assembled into an app, with one instance of the *Buffer* and *Drill* concepts for each session, the *Drill* concept instantiated so that its tasks are exercises and parts from the *Exercise* concept, and one instance of the *Chatbot* concept for each combination of session and task:

	app Tutor
	includes	  
	  Session
	  Email
	  Exercise
	  Advice [Exercise.Competency]
	  Buffers: Session.Session -> one Buffer
	  Drills: Session.Session
	    -> one Drill[Exercise.Exercise + Exercise.Part]
	  Chatbots: Session.Session -> Drills.Task -> Chatbot

**Note**. There will actually only be one chatbot instance per subtask, even though the declaration suggests one per task.

When a user opens a new session, their email address is registered:

	sync open (c: Client, addr: Email.Address, out s: Session.Session)
	  Session.open (c, s)
	  Email.register (s, addr)

When a user selects an exercise, a new task is selected associated with that exercise, and the user’s buffer is cleared:

	sync selectExercise (c: Client, out s: Session,
	     ex: Exercise.Exercise, out exerciseText: Text)
    Session.getSession (c, s)
    Exercise.getExercise (ex, exerciseText)
    Drills[s].selectTask (ex)
    Buffers[s].open ("")

The text associated with the exercise (*exerciseText*) will be displayed to the user.

When a user selects a part of an exercise, a new subtask is selected associated with that exercise, the user’s buffer is reset with the content of the prior solution to that part (or an empty string if none), the advice stored for the competency associated with that part is obtained, and the associated chatbot is prompted with that advice and the textual description of the exercise part:

	sync selectPart (c: Client, out s: Session,
	     ex: Exercise.Exercise, part: Exercise.Part,
	     out comp: Exercise.Competency,
	     out partText: Text, out advice: Text)
	  Session.getSession (c, s)
	  Exercise.getPart (ex, partText, comp, part)
	  Drills[s].selectSubTask (ex, part)
	  Buffers[s].open (emptyIfNone(part.solution))
	  Advice.getAdvice (comp, advice)
	  Chatbots[s][part].prompt (advice ^ partText)

When a user saves the buffer, the content is submitted as a solution (to the current part), that solution is presented as a query to the relevant chatbot, and the solution is also emailed to the user:

	sync submitSolution (c: Client, out s: Session,
	     out solution: Text)
	  Session.getSession (c, s)
	  Drills[s].submit (solution)
	  Buffers[s].save (solution)
	  Chatbots[s][p].ask (solution)
	  Email.send (s, solution)

**Notes**. In this current formulation, the query to the chatbot includes only the solution to the current part of the exercise, but perhaps it should contain the (derived) current version of the entire exercise solution. The chatbot will respond to the query spontaneously; no sync is needed to describe that.

# Summary of Concept Design Ideas

Here are some of the principles that guided the conceptual design:

- **Factoring**. Reasons for factoring out concepts include: standard reusable functionality (*Session*); separable/optional feature (*Email*); separable feature with considerable complexity (*Buffer*, *Chatbot*); to exploit lifting so that concept need not include indexing (*Drill*); functionality that is separable and may be subsequently elaborated (*Advice*).
- **Lifting**. Whenever a concept might have included an indexed state component, but the different indexed parts of the state never interact, the concept should instead be lifted. For example, each buffer has its own behavior, and there are no actions across buffers, so rather than having the *Buffer* concept include an indexed collection of buffers, the concept itself is instantiated, indexed over sessions. *Email* should probably be lifted too.
- **Abstraction**. Details of a concept are excluded if they are not relevant to the essential design questions. For example, *Buffer.edit* embodies all the complexity of a text editor, but for the purpose of high level design, it’s best just to assume this.
- **Polymorphism**. Concepts are made polymorphic when possible. For example, the tasks of the *Drill* concept and the competencies of *Advice* can be bound to any types.
- **Concept vs sync design**. In some apps, the design of the concepts themselves will be challenging. In this case, the main problem was how they fit together, so laying out the essential actions and figuring out how they would be synced was primary.

# Open Design Questions

The concept design offers only a draft attempt at resolving the tricky questions about how the various parts of the app should interact. Particular decisions embodied in the concept design that should be revisited include:
- Associating chatbot sessions with parts of exercises.
- Deriving exercise solutions from part solutions, and not allowing them to be edited explicitly.
- Conveying solutions to user by emailing submitted solutions to subparts. Clearly, users may prefer an explicit action for sending or saving a solution, which should probably send the entire exercise solution. There should also be a way to send the chatbot transcript.
- Associating prompts with competencies tied to parts, and not with whole exercises, and feeding the text of the exercise part as part of the prompt.
- The chatbot instances are associated with parts of exercises, and their sessions persist, so that if you select a part, then select another part and come back to the first, you’ll be in the same session.
- ...