---
author: "Daniel Jackson"
title: "Notes on design of a GPT-powered tutor"
date: "2023-05-11"
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
	  new (p, s); ask (s, t); respond (s, t); ...
	state
	  prompt: Session -> one Text
	  questions, answers: Session -> seq Text
	actions
	  new (prompt: Text, out s: Session)
	  	// create new session with given prompt
	  ask (s: Session, t: Text)
	  	// user enters text
	  respond (s: Session, t: Text)
	  	// bot responds
	  close (s: Session)
	  	// needed?

The operational principle says that a new session is created with a prompt; in our design, this will turn out to be hidden from the user. The user then enters some text (modeled by the *ask* action) and the bot *responds*.

The prompt will turn out to be a concatenation of distinct components, such as a general tactic for the bot to follow; a pedagogical context for the exercise at hand; and the starting seed for the exercise itself (eg, “ask the user to do X”).

The student will work in a mutable buffer, generating a sequence of immutable texts:

	concept Buffer
	purpose create immutable text with mutable buffer
	principle
	  // if you create a buffer and edit it,
	  // saving produces the last content
	  new (b); edit (b, t); save (t') {t' = t}
	state
	  content: Buffer -> one Text
	actions
	  new (out b: Buffer)
	  	// create new buffer
	  edit (b: Buffer, t: Text)
	  	// edit buffer so new content is t
	  save (b: Buffer, t: Text)
	  	// save contents of buffer
	  close (b: Buffer)
	  	// needed?
	  	
The *save* action is intended to be sync’d with another concept; I’ve factored out the means by which the text is saved.

The chatbot is fueled by a repository of advice, which I’ll model as a concept that offers advice on various topics:

	concept Advice [Topic]
	purpose manage repo of advice
	principle
	  // create topic, add advice, then getting
	  // advice will return all advice added to that
	  // topic
	  newTopic (n, t); addAdvice (t, a1)... addAdvice (t, aN); get (t, a') {a' = a1 + ... + aN}
	state
	  advices: Topic -> set Text
	  name: Topic lone -> one Text
	actions
	  newTopic (name: Text, out t: Topic)
	  addAdvice (t: Topic, advice: Text)
	  getAdvice (t: Topic, out advices: set Text)

**Side notes**. This is a strange concept because it has so little behavior but it seems to represent a very significant part of the tutor, and would likely have its own code. I’ve included an action *getAdvice* to help reify the operational principle even though the state is visible and observer actions aren’t generally needed (but I think I should rethink this for OP-relevant state observations). At first, I incorporated advice into a concept that managed exercise/part structure, but then when I realized that that concept needed to incorporate the student behavior, it became too complex and I decided this was a better concept to factor out than a student behavior concept. Could this concept be made more similar to one that already exists, such as a Q&A concept of the sort you might use to model StackOverflow etc? In the AI tutor setting, the advice will be given to the chatbot, not the student, but this should be irrelevant in the design of the concept.

The pedagogical content is organized as a collection of exercises with multiple parts; the parts are associated with competencies (which will be the topics for advice).

	concept Exercise [Competency]
	purpose maintain and offer structured exercises
	principle
	  // admin creates exercise with parts
	  // when student selects part, they get 
	  // the relevant descriptions
	  newExercise(te, e); newPart (e, tp, c, p);
	   selectPart (e, p, t', c') {t' = te^tp, c' = c}
	state
	  parts: Exercise one -> set Part
	  competence: Part -> one Competency
	  description: Exercise + Part -> one Text
	actions
	  newExercise (t: Text, out e: Exercise)
	  newPart (e: Exercise, t: Text, c: Competency, out p: Part)
	  selectPart (e: Exercise, p: Part, out t: Text, out c: Competency)
	  // when student selects a part of an exercise,
	  // they're given a description that combines
	  // the description of the exercise with the 
	  // description of the part (and the competency
	  // they'll acquire)
	 
Notes
- Both exercises and their parts have descriptions
- Competency associated only with parts and not whole exercises. This will mean we can’t associate advice with exercises as a whole. Perhaps advice common to all parts is just included for those parts?
- Parts are not shared between exercises
- Considered including student behavior in this concept to make it richer but then decided it was more important to factor it out so that the student behavior concept could be lifted over students (that is, each student behavior being independent)

Concept for student behavior:

	concept Drill
	purpose govern student work blah...
	principle
	  // if you select a task and one of its subtasks
	  // and then submit a solution, it's recorded
	  // appropriately
	  selectTask (t); selectSubTask (t, t'); 
	  submit (s) {t' in t.subtasks and t.solution = s}
	state
	  subtasks: Task one -> set Task
	  solution: Task -> lone Text
	  current: lone Task
	actions
	  selectTask (t: Task)
	  // set current to t
	  selectSubTask (super, sub: Task)
	  // set current to sub
	  // ensure sub a subtask of super
	  submit (t: Text)
	  // current.solution := t

Notes
- Decided to make this cursor-based; not sure this is a good idea, but seems useful to have this state somewhere in the app.
- OP is not very satisfying but at least suggests how concept is used.

## Composing concepts

	app Tutor [Student, Competency]
	includes	  
	  Buffer
	  Advice [Competency]
	  Exercise [Competency]
	  Drills: Student -> Drill

Notes
- drills is a set of concept instances indexed by student.
- How to deal with type that has no allocator in any concept? For now, just making such types parameters of the app itself and will assume that they get allocated in syncs.

Example of a sync:

	sync selectPart (s: Student, e: Exercise.exercise, p: Exercise.Part)
	  // get part's text to present to student
	  Exercise.selectPart (e, p, t, c)
	  // get advices associated with competency
	  Advice.getAdvice (c, as)
	  // prompt chatbot with concatenation of advices	ChatBot.new (concat(as), session)
	  // where does the session go?
	  // set the student context for answering
	  Drills[s].selectSubTask (e, p)

	  Buffer.new (b) // where does the buffer go? 

