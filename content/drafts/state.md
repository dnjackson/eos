## Motivation: Defining Behavior

Using the notion of a state machine, we can define how a concept will behave in every possible situation. 

## The Role of State

The state of a concept plays a very straightforward role, and that’s to determine what the concept *remembers* at runtime. The state is the concept’s *memory*. If some piece of information is needed in an action, then it will have to be in the memory; if it will never be needed, it shouldn’t be there, even if it’s interesting.

For example, suppose we’re designing a concept called *Group* for messaging within a group of users. The state will certainly need to include which users belong to which groups, because there will be actions that depend on this (the action *post* for posting a message will likely check that the user is a member of the group they’re posting in). The state will also need to distinguish regular members of a group from the owner of the group, since some actions (such as approving new members) will only work for owners. Should the state also include the date at which a user joined a group? That would depend on the details of the behavior. If a user who joins a group can only see messages posted after they joined, the date will be necessary, since otherwise this check can’t be performed. But if a user can see all old messages, the date wouldn’t play a useful role, and can be omitted.

## Defining State

We’ll define the state of a concept as a series of variable declarations. Each variable will correspond to a set, or to a relation. A relation may map one set to another (in which case it’s called a *binary* relation), or may associate elements of more than two sets (in which case I call it a *multirelation*). If you think of a relation as a table, then a binary relation has two columns, and a multirelation has three or more. A set is just a table with one column, so it’s a relation too.

This way of thinking about state is taken from the Alloy language, and we’ll use some basic Alloy-like syntax to define the relations.

For example, suppose we are designing a concept called *Invitation* that captures the kind of functionality that appears in many apps in which one user can issue an invitation to another user, to be a friend, to access some files, or whatever. The state of the concept might include 

	invited: User -> User

which introduces a relation called *invited* between users. You can think of a relation like this in several ways: as a *mapping* from users to users which can be access by a lookup (where *u.invited* might represent the set of users invited by the user *u*), as a *tuple set* or *predicate* that contains the tuple *(u1, u2)* when user *u1* has invited user *u2*; or as a *table* with two columns, with the understanding that the first column represents the inviting user and the second the invited user, and each row represents an invitation. These are all equivalent ways to view a relation.

If we want to store the date on which invitation was issued, we could use a three-way relation:

	invited: User -> User -> one Date

where *(u1, u2, d)* would be included when user *u1* invited user *u2* on date *d*. (The multiplicity keyword *one* says that each pair of users maps to a single date.)

Another way to model this would be to introduce a set of invitation objects, and then we can define the date of an invitation and who it’s from and to as additional projections:

	from, to: Invitation -> one Person
	date: Invitation -> one Date

As an example of a set declaration, we might have a set of pending invitations that have yet to be accepted:

	Pending: set Invitation

Novices sometimes wonder why it’s preferable to use a set rather than a mapping to a boolean value. Why not, for example, represent the pending invitations as a relation like this?

	isPending : Invitation -> one Bool
	
The answer is that it’s much easier to write relational expressions about the state using the set rather than the boolean function. For example, the set of users who have issued invitations that are pending can be written as *Pending.from*; to write this with the boolean function you’d need to construct a set comprehension with a formula.

Also, introducing sets lets you include certain invariants in the declaration of the state. For example, suppose the Invitation concept includes a *remind* action which reminds a user who has received an invitation to accept or reject it, and this action is triggered some fixed time after the invitation is issued. We could represent the time for the reminder like this:

	remindOn: Pending -> one Date

which says that each pending invitation has a reminder date. Without the *Pending* set, we’d need to associate the reminder date with all invitations:

	remindOn: Invitation -> lone Date

which says that each invitation is associated with zero or one dates at which the reminder should occur. Because the declaration maps all invitations, not just pending invitations, we have to weaken the multiplicity from *one* to *lone* (zero or one), and the invariant that says that every pending invitation has a reminder date is no longer expressed.

## Principles

Here are some principles to help you design the state of a concept:

**Sufficiency**. The state must be rich enough to support the actions. Put another way, the concept must remember enough about actions in the past to be able to perform actions in the future correctly. Example: an *Upvote* concept, which tracks how many times users have approved some item, must remember which user issued each upvote in order to prevent double voting, but the date of the upvote would likely not be needed.

**Necessity**. Generally, the state should not include components that are not necessary to support the actions. Example: a *Reservation* concept need not store reservations that have been canceled; a *Trash* concept need not store the original location of an item if there is no *restore* action; a *Voting* concept need not store the time at which a vote occurred, if all votes received before some deadline are treated equally.

There is a subtle qualification to this principle. Sometimes it is useful to enrich the state in ways that anticipate future functionality. Example: in a *Moderation* concept, there might be no actions that require storage of posts that have been rejected by a moderator, but it might be sensible to store them anyway if you anticipate adding, for example, functionality that recommends acceptance or rejection based on treatment of prior posts from the same user.

**Most abstract structures**. The state should not include structural decisions that needlessly complicate the state and limit the freedom of the implementer. In particular, don’t use a sequence (ordering some elements) when a set suffices. Example: you might think that the messages in a *Group* concept should be stored as a sequence, but since none of the actions involve sequence manipulations, and each message can appear only once, a set of messages is better. If you want the concept to contain enough information for messages to be displayed in order, you can associate a date with each message and assume the mapping of the concept to the user interface sorts messages by date.

**Abstract types**. Don’t use a primitive type that has properties that aren’t necessary for the concept behavior. Example: a *Ticket* concept may generate identifiers for tickets which, in the implementation, will happen to be integers that are generated in order. In the concept design, however, all that matters is that the ticket identifiers are distinct, so you should introduce an abstract type *TicketId* to represent them rather than using *Int*.

**Richer abstract types**. Another reason to introduce an abstract type is to record in the design that a type will have *more* properties than a primitive type. Example: rather than representing an email address as *String*, represent it as an abstract *EmailAddress* type, to capture the fact that email addresses have a particular form that can be validated.

**Primitive types**. The standard primitive types are assumed to be defined globally, so a concept can refer to types such as *String*, *Integer*, *Bool*, etc., and use their standard operations (such as concatening strings, adding integers) in defining actions.

**Sequences**. When an ordered collection of elements is needed, a sequence can be used. Example: in a *Chatbot* concept, the transcript is a sequence of queries and responses:

	Query, Response: set Entry
	transcript: seq (Query + Response)

(The plus is a union operator, so *Query + Response* is the set that includes both queries and responses.)

**Concepts aren’t classes.** A common misunderstanding is to think of a concept as a class that has instances, with the concept’s state variables being the instance variables of the class. Example: you might write

	concept User
	  state
	    friends: set User

imagining a collection of user objects each of which points to a set of friends. This is wrong, and assumes a much more complicated set up in which types occurring in state declarations can themselves be concepts. The correct view is that a concept is a state machine whose non-primitive types denote objects only in the sense that they have identity, not in the object-oriented sense of carrying methods and so on. The correct way to write this concept is

	concept Friend <User>
	  state
	    friends: User -> User

which introduces a *Friend* concept that stores a friend graph.
