# Concept Configuration

In concept design, a running app is viewed as a configuration of concept instances. I’ve been thinking about how these configurations should be designed, and had planned to write a short blurb about it. But as I wrote it, I realized there was more to say than I’d expected, and also more to figure out.

So I thought I’d share it with some friends for feedback. I’d welcome help and comments from all you concept designers out there, but I’m thinking particularly of the following people whose recent work is connected to these ideas:

- **Kevin Sullivan**, who’d argued, in our collaborative work on concept design for [moral distress](https://pubmed.ncbi.nlm.nih.gov/37336696/), that surveys and temperature readings could be linked externally in the application state. I’d resisted this but now I think Kevin was right all along.
- **Abutalib (Barish) Namazov**, whose concept-driven, LLM-based code generator [Kodless](https://www.youtube.com/watch?v=WgOhtH3lugk&list=PLY_nsiUEgM3nbxcO3_ONmKL-ZRhLKpfGV) might exploit configurations as a construction strategy.
- **Geoffrey Litt**, whose [Patchwork](https://www.inkandswitch.com/patchwork/notebook/) system corrects a flaw of Github’s and binds chats to branches rather than pull requests. A discussion with Geoffrey prompted me to write this piece.
- **Alcino Cunha**, whose work on [software configurations](https://www.researchgate.net/publication/382363748_Designing_Software_with_Complex_Configurations) separates regular state from configuration state. The ideas here might help make that distinction more precise, and the configuration-enhanced version of [Alloy](http://alloytools.org) might help analyze configurations in concept designs.

## Concept Instances & Extents

**Concepts manage sets.** A concept instance handles *sets* of things. For example, the Label concept, which is used in Gmail for organizing messages, contains the functionality for creating labels, associating them with messages, and finding messages with particular labels. Only one instance of the concept is needed to represent these sets and relationships.

**OOP is different.** In contrast, the instances (or objects) of a Label class in an object-oriented program would each represent a single label. You could have each label object hold links to the associated messages, and this would make it easy to find the messages with a given label.

**OOP still needs concept-like obejcts**. In an object-oriented program, you’d still need some way to navigate from label names entered by the user to the actual labels. For this, you’d probably define another class, say LabelRegistry, whose objects contain mappings from strings to label objects. Or you might add a static mapping to the Label class, although this is a bad idea because, as we’ll see below, you’re likely to want to have more than one such mapping. Either way, this simple example shows how the relationship between domain concepts and objects or classes in object-oriented programming isn’t as simpler as people sometimes claim, and more often than not a single domain concept is implemented as a *collection* of classes.

**How many instances per concept?** In the simplest cases, having a single instance of each concept will suffice. But often multiple instances will be needed, to correspond to different, disjoint contexts. In the Label case, different Gmail users will have different instances, so that their labels aren’t confused.

**Concept extent.** Each instance of a concept has an *extent* that determines its lifetime and span, and is defined by some other concept (or object) instances it’s associated with. In Gmail, for example, the extent of each Label instance is a single User. Two distinct users will have different Label instances so that their labelings will never interact (in particular, labels aren’t included in sent messages), and the same Label instance will be associated with a given user for that user’s lifetime.

Deciding on a concept’s extent is often a critical design question. Apple’s macOS file system, for example, has a Label concept, with labels called “tags,” but there seems to be just one instance across multiple users, with some [surprising consequences](https://discussions.apple.com/thread/5479755).

## Zoom: Design Snags with Chat Extent

Zoom’s Chat concept shows how concept extents can have serious impacts on usability. There are of course many instances of the Chat concept in Zoom, since otherwise all users would have to be within a single chat. But what exactly is the extent of a Chat instance?

**Not just a meeting.** At first glance, it seems that the extent of a chat is a single meeting. But this isn’t quite right. A Zoom meeting can be restarted multiple times, even one that wasn’t defined as “recurring” when it was created. When you join the restarted meeting, you won’t see any messages from the chat of the previous session. So each restart associates a fresh chat instance with the meeting. In other words, the extent of each chat instance is a meeting *session*.

**Private chats.** When you post a message in Zoom, you can optionally send it privately to a single participant. One could view this as a special feature of Zoom’s Chat concept. But a simpler (and more robust) interpretation is that there are separate chat instances for each pair of participants. In this case, the extent of a private chat is defined by a tuple (meeting-session, participant, participant).

**Breakout rooms.** Finally, a chat extent issue that reflects a serious design flaw in Zoom. When you open breakout rooms in a Zoom session, each room gets its own chat instance. If this were an additional instance, it might be justified---as a means of allowing the members of the breakout room to converse privately. But unfortunately it’s the *only* instance that is accessible.

The consequences of this design decision are disastrous. Since the chat instance of the main meeting session is no longer accessible, instructions or background information for a breakout room cannot be posted in the session chat. Moreover, the host of the meeting, not being a member of the breakout room chats, cannot post a chat message to signal the upcoming end of the breakout, for example. One [guide](https://history.washington.edu/tutorial-zoom-breakout-rooms) to using breakout rooms in teaching recommends a tedious workaround:

> Students will no longer have access to anything in the main room once the breakout session has begun (including the chat and the whiteboard). Consider posting the discussion objectives/prompts on Canvas and ensuring each student has Canvas up in another window before the breakout session begins.

**Fixing breakout rooms.** To mitigate this obvious flaw, Zoom added the concept of Broadcast to allow a host to send a message to breakout rooms. This concept needlessly replicates some of the features of the Chat concept, while missing other important features (such as persistence, and the ability to copy and paste from messages, or to click on links).

## More Extent Examples

Can you suggest other interesting examples?

- In Github, a chat instance is associated with a pull request. This means the conversation must end if a pull request is rejected or withdrawn, and cannot start until the request has been issued. [Thanks to Geoffrey Litt for this example.]
- TBC.

## Lifting: A Design Move

**Enriching concept state.** An alternative to having multiple instances of a concept is for the state of the concept itself to be enriched with mappings.

For example, a single instance of the Meeting concept could allocate multiple meeting objects, and manage their association with chats. This would be a bad idea for two reasons.

First, it would make the Meeting concept dependent on the Chat concept: if we wanted to build a system that had meetings but not chats, such as meeting concept would not be suitable (and making an associated chat optional would not solve this).

Second, the meeting-chat association is not an intrinsic part of either concept, but is a property of how they are configured together. Including it in Meeting corrupts and needlessly complicates the concept.

**Contexts: a bad idea**. In an earlier attempt to model Zoom, I augmented the Chat concept with a “context” representing the extent of the chat. Because this context is polymorphic, and because it’s plausible that every chat has some context, it doesn’t seem to introduce a dependence (which augmenting Meeting would). But it nevertheless feels like a kludge that complicates the concept by adding functionality that is not concept-specific, and now seems like a mistake.

**Lifting as a design move.** We can capture this idea as a design move that I call *lifting*. Whenever a concept state includes relational mappings, we can consider moving a mapping outside the concept into application configuration state.

For the meeting-chat example, if we started with this:

	concept Chat [Context]
	state
		chat: Context -> lone Chat
		posts: Chat -> set Post

	app Zoom
	includes
		Meeting
		Chat [Meeting]

we can lift to this:

	concept Chat
	state
		posts: set Post

	app Zoom
	includes
		Meeting
		ch: Meeting -> lone Chat

In abstract terms, what’s going on is that we have a state relation of the form

	Meeting -> Chat
	
that can either be expressed like this

	[Meeting -> Chat]

with square brackets denoting state stored inside a concept, or, in lifted form, like this

	[Meeting] -> [Chat]

## How Lifting Simplifies Concepts

The importance of lifting is that it simplifies a concept by reducing its scope.

**Simple lifting example.** For a more basic example, and setting aside the extent question for now, suppose we were modeling the meeting concept and were considering something like this

	concept Meeting
	state
		meetings: set MeetingObject
		created: meetings -> one Date
		...

in which the concept maintains a set of meetings (represented by members of the allocated type MeetingObject), and mappings to associate each meeting with its creation date, etc.

This is a poor design, because all the functionality of the meeting concept can be described within the span of a single meeting. There are no actions that merge meetings, for example, or any invariants that would limit the properties of a meeting due to the presence of other meetings. Lifting fixes this, and results in a simpler concept state:

	concept Meeting
	state
		created: Date
		...

**Invariants prevent lifting.** Now suppose we’d included meeting links in the meeting concept, like this:

	concept Meeting
	state
		meetings: set MeetingObject
		link: meetings lone -> one Link
		created: meetings -> one Date
		...

This introduces an invariant (indicated by the lone keyword in the link declaration) that links are unique, with no two meetings sharing a link.

To maintain this invariant, the concept needs access to the set of all meetings and their links. Applying lifting here would mean maintaining this invariant in the synchronizations, violating the concept design principle that all significant functionality is held inside concepts.

**Having your cake and eating it.** But lifting still seems desirable for the other aspects of meeting behavior, which are all scoped within a single meeting. A solution to this dilemma is to separate out the link assignment functionality in a separate concept, and then lift the meeting concept. This would be especially compelling if the link assignment functionality has uses in other situations and thus has a claim to being a concept in its own right. And indeed, this is the case, and the concept, called Capability, is a widely used scheme for granting access to resources through unique keys.

## When Non-Lifting Reveals Complexity

Sometimes considering whether a concept can be lifted reveals an interesting design complexity.

**Can reservation be lifted?** Take the Reservation concept, for example. Reservations associate users with resources (for example, restaurant patrons with tables), but we also need to represent the fact that resources have owners (the restaurants that own the tables).

Must the Reservation concept include ownership of resources in its state? Or can we lift this? In the lifted version, we would have one instance of the Reservation concept for each business that takes reservations. 

This seems promising. The tables of one restaurant or the rooms of one hotel are not coupled in any way to the tables of another restaurant or the rooms of another hotel. And indeed many restaurants and hotels _do_ run their own reservation systems, so it seems that separate instances for each business should make sense even for a service that manages multiple businesses.

**Conflict detection.** But there’s a feature of reservation services that *does* actually couple the businesses together. In order to prevent consumers who want to preserve their choices from gaming the system by making multiple reservations and then dropping all but one, these services prevent such reservations from being made.

Apps like OpenTable and Resy, for example, prevent you from making two reservations on the same night. This successfully rules out foul play, but it frustrates legitimate demands  too (such as wanting to book a bar for a cocktail and then a restaurant for a meal, as [this aggrieved user of Resy explains](https://www.reddit.com/r/FoodNYC/comments/11yumbw/whats_up_with_resy_not_letting_u_book_two_things/)).

So, in its current form, the Reservation concept cannot be lifted if it is to support this kind of multiple booking.

**Challenge to readers.** Is there a neat way to factor this behavior out into a separate concept (whose purpose is to detect and prevent abuse), in the same way that the Capability concept was used to factor linking out of the Meeting concept?

## Concept code

To make concrete the explanations above, here’s the Zoom meeting-chat design in the formal concept notation.

	concept Chat [User]
	state
		posts: set Post
		date: Post -> one Date
		by: Post -> one User
		msg: Post -> one Text
		members: set User
		joined: User -> one Date
	actions
		new (out c: Chat)
		join (u: User)
		leave(u: User)
		post (u: User, t: Text, out p: Post)
		delete (u: User, p: Post)
		
	concept Meeting [User]
	state
		host: User
		participants: set User + Endpoint
		active: bool
	actions
		new (h: User, out m: Meeting)
		start (u: User)
		end (u: User)
		join (u: User + Endpoint)
		leave (u: User + Endpoint)
		
	concept Capability [Resource]
	principle
		after allocate, can get
		(and can't access without get)
	state
		used: set Key
		private resource: used -> lone Resource
	actions
		allocate (r: Resource, out k: Key)
			k not in used
			used += k
			k.resource := r
		get (k: Key, out r: Resource)
			r := k.resource
		
	concept Auth
	state
		username: User -> one Text
		password: User -> one Text
	actions
		register (n, p: Text, out u: User)
		authenticate (n, p: Text, out u: User)
		changePassword (u: User, p: Text)
		delete (u: User)
		
	app Zoom
	includes
		let U = [Auth.User], M = Meeting [U],
			C = Chat [U], K = Capability [M].Key
		au: Auth
		ca: Capability [M]
		ch: M -> lone C
	syncs
		createMeeting (host: U, out k: K, out m: M)
			M.new (host, m)
			ca.allocate (m, k)
		startMeeting (host: U, k: K, out m: M)
			ca.get (k, m) 
			m.start (host)
			C.new (c)
			m.ch := c // replaces on restart
			c.join (host)
		postInChat (m: M, u: U, t: Text, out p: C.Post)
			m.ch.post (u, t, p)
		
**Notes**
- I haven’t introduced an explicit session type corresponding to Zoom meeting sessions. Instead, chats are bound to meetings, and the binding is reassigned on start.
- I’ve used uppercase names (eg, C, M, U) as shorthands for types, and lowercase names (eg, ca) for concept instances.
- The *new* action allocates a fresh instance of the concept.
- Previously, I’ve often overloaded names, using the same name for a concept and for a type used in that concept. For example, in the unlisted version of the Meeting concept, I might have used the name Meeting both for the concept and for meeting objects within the concept. I now think this was a bad idea. So the name Meeting within the new action of the Meeting concept refers only to the concept type itself (and is the type of the instance returned by the action).

Here’s the variant in which the chat is associated with the meeting and persists across sessions of that meeting:

	createMeeting (host: U, out k: K, out m: M)
		M.new (host, m)
		Ca.allocate (m, k)
		C.new (c)
		m.ch := c
		c.join (host)
	startMeeting (host: U, k: K, out m: M)
		ca.get (k, m) 
		m.start (host)

Note that the declaration of the application-level binding is unchanged, but the chat is now allocated and bound to the meeting when the meeting is created rather than when the meeting is started.

And here’s the variant for private chats:

	app Zoom
	includes
		let U = [Auth.User], M = Meeting [U],
			C = Chat [U], K = Capability [M].Key
		au: Auth
		ca: Capability [M]
		ch: M -> lone C
		privateCh : M -> U -> U -> lone C
	syncs
		privatePost (m: M, from, to: U, t: Text, out p: C.Post)
			if no privateCh [m, from, to]
				C.new (c)
				privateCh[m, from, to] := c
				privateCh[m, to, from] := c
			privateCh[m, from, to].post (u, t, p)
	
	