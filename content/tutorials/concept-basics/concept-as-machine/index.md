---
author: "Daniel Jackson"
title: "Concepts are state machines"
weight: 6
date: "2022-09-14"
description: ""
ShowToc: false
TocOpen: true
hideMeta: false # removes date etc from post
summary: ""
editPost:
    URL: "https://forum.softwareconcepts.io"
    Text: "Comments" # edit text
    appendFilePath: false # to append file path to Edit link
---
As I explained in another [tutorial](../apps-are-state-machines), the easiest and most effective way to define the behavior of an app precisely is to model it as a state machine. The app has a set of possible states, and actions that update and query the states. An execution of the app is just a sequence of actions (or more correctly, action instances that include particular action arguments), and the behavior as a whole is the set of all possible executions.

Describing concepts themselves as state machines brings the same advantages. The state machine of the app as a whole is just a combination of the machines of the individual concepts. This is how concepts bring modularity to thinking about the functionality of an app.

## An example of a concept as a state machine

Let’s model the core concept of [Yellkey](https://yellkey.com), a URL shortening service that many teachers use. The novelty of Yellkey is that the short URLs it generates use common English words, so you get short URLs like “https://yellkey.com/hello” rather than ones that contains strange sequences of letters that are harder to transcribe. 

Here’s the Yellkey concept as a state machine:

	concept Yellkey
	purpose shorten URLs to common words
	principle
	  after registering a URL u for t seconds
	  and obtaining a shortening s, looking up s
	  will yield u until the shortening expires
	  t seconds from now:
	  register (u, t, s); lookup (u, s') {s' = s}
	state
	  used: set String
	  shortFor: used -> one URL
	  expiry: used -> one Date    
	  const shorthands: set String
	actions
	  // register URL u for t seconds
	  // resulting in shortening s
	  register (u: URL, t: int, out s: String)
	    s in shorthands - used
	    s.shortFor := u
	    s.expiry := // t secs after now
	    used += s

	  // lookup shortening s and get u back
	  lookup (s: String, out u: URL)
	    s in used
	    u := s.shortFor

	  // system action: shorthand s expires
	  system expire (out s: String)
	    s.expiry is before now
	    used -= s
	    s.shortFor := none
	    s.expiry := none
	
Actions are a good place to start because they capture what the user does to use the concept. There are two actions that are performed by the user: **register**, which takes a URL and some duration in seconds,  and returns a shortening; and **lookup**, which takes a shortening and expands it by returning a URL. In Yellkey, the user doesn’t get to choose an arbitrary duration, but selects one from a dropdown (which has a few options between 1 and 24 hours).

The fact that a shortening can expire is represented by an action that is performed by the system: **expire** occurs for a given shortening when it has passed its registered duration.

## Defining state

To design the state, you consider what must be remembered by the concept in order to support the actions. In this case, what’s needed is: a mapping from shortenings to URLs, and a mapping from shortenings to their expiry times. Since the shortenings must be drawn from a set of common words, that set is stored as part of the state also.

For convenience, I’ve also included a component that remembers which shortenings are currently being used. Strictly, this isn’t necessary because the shortenings in use are just the ones mapped by the two mappings. But this makes it a bit easier to define the actions, and to make explicit (in the declarations of the mappings) that every shortening being used maps to exactly one URL and exactly one expiry time.

## Defining Actions

Each action has a header that names the action and lists some arguments, and a body that defines the action’s behavior:

	register (u: URL, t: int, out s: String)
	  s in shorthands - used
	  s.shortFor := u
	  s.expiry := // time now plus secs
	  used += s

The arguments that are marked with the keyword *out* are outputs that are returned by the action; the others are all inputs. (Using a keyword rather than having a special return value turns out to be more convenient, because it allows more than one output, and introduces names for them).

The body includes two kinds of statements: preconditions that limit when the action can occur, and postconditions that say what effect the action has.

Let’s look at each line in turn. The first line 

	s in shorthands - used

says that any value can be picked for s that is in the set *shorthands - used*, that is the set of words that are shorthands but not used. 

The second line

	s.shortFor := u

says that the mapping between shortenings and their URLs is updated so that the new shortening s maps to the given URL u.

The third line

	s.expiry := // t secs after now

says (informally) that the mapping between shortenings and their expiry times is updated so that the new shortening s maps to the time corresponding to t seconds after the current time.

And finally

	used += s

says that the set of used shortenings has s added to it.

**A note on notation**. I’ve used a simple notation for the action formulas based on [Alloy](https://alloytools.org), extended with some C-style operators. Given a relation *r: A -> B*, and variables *a* and *b* holding values drawn from the sets A and B, the statement *a.r := b* makes *a* map to *b* in *r*—that is, adds the pair (a, b) to r.

## Non-determinism

The register action doesn’t say how the shortening is chosen. It says any shortening is acceptable so long as it’s in the set of shorthands that are not currently being used.

This is called “non-determinism”. It doesn’t mean that the selection has to be random, or that if you could somehow undo the action and do it again that you might get a different result.

All it means is that the specification doesn’t say how the shortening is chosen, and whoever implements the concept can decide how it’s done (so some prefer the term “under-determined” to “non-deterministic”). 

For example, the set of shorthands could be represented as a ring with a pointer to the last shorthand issued.  When register is called, it returns the next shorthand in the ring, or skips it if it’s being used, carrying on round the ring until it finds an unused one.

## Design is in the details

*The details are not the details. They make the design*.—Charles Eames

Being precise about the states and actions helps in several ways. The first is communication: it helps you convey the concept behavior unambiguously to others.

The second is that it counters *wishful thinking*. As anyone who’s written software knows, it’s easy to convince yourself that some function is obvious until you actually try and code it, and then you discover you don’t even understand what it should do. Writing a model is like writing code in this respect, except that it’s less work and lets you know sooner that you’re confused.

The third is that different options come up as you formalize the behavior, prompting you to address design questions you might not even have noticed.

For example, an alternative specification of the register action might not just *add* a new shortening for a URL but might *replace* any existing ones.

On the one hand, one could argue that it’s not in the spirit of Yellkey to support multiple shortenings for a single URL, and it would also save resources to do this. 

On the other hand, this would open up a potential malicious attack. Suppose a lecturer writes a Yellkey short URL on the board for a class quiz. A naughty student in the class could register a new one for the same URL, causing the old one to stop working.

## Concepts aren’t classes

People are sometimes confused about the difference between concepts and classes in object-oriented languages.  With our concept example in hand, we can now see how different a concept is from a class.

- **User-facing, end-to-end functionality**. A concept provides user-facing functionality, in an end-to-end way. A class, in contrast, is usually hidden from users, and provides only a piece of functionality that is combined with functionality in other classes to deliver some value to the user. For example, an implementation of Yellkey might have a *Shorthand* class each of whose instances holds a shorthand and its associated URL. But this class doesn’t offer lookups; that would have to be in another class.
- **No intrinsic dependencies**. Concepts don’t have [intrinsic dependencies](../dependency) on other concepts, and can be understood in isolation. Classes almost always use other classes, and can’t operate without making calls to them.
- **System actions**. Concepts can have both user and system actions; the methods of a class are called from outside (and if they were called internally, the call would not be visible to the outside).
- **Richly associative state**. Concepts hold state that associates different objects and values in rich ways, and allows flexible navigation. Classes, in classic OOP, are more constrained. A class is represented by a set of objects, and each object can contain references to other objects or collections of objects. But this structure does not support querying for particular objects within a class. For example, as I just noted, given a *Shorthand* class whose objects represent registrations of shorthand/URLs, you can’t ask for the objects that match a particular shorthand. The OOP workaround for this is to introduce another class whose instances are shorthand-to-URL mappings.

None of this means that concepts can’t be implemented in an object-oriented language. In fact, a reasonable approach is to implement each concept as a class, supported by whatever other classes are needed to fulfill its functionality (but avoiding any references to the classes of other concepts).
