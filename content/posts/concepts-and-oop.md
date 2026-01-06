---
author: "Daniel Jackson"
title: "Why concepts aren't objects"
date: "2025-12-29"
description: "Object oriented modeling and its pitfalls."
ShowToc: false
TocOpen: false
hideMeta: false # removes date etc from post
# hideSummary: true
summary: "Object oriented modeling and its pitfalls."
editPost:
    URL: "https://forum.essenceofsoftware.com"
    Text: "Comments" # edit text
    appendFilePath: false # to append file path to Edit link
---

**Summary**. *Concepts should not be confused with objects in object-oriented design. While objects model individual entities with identity (a universally applicable idea), object-oriented thinking goes further, and forces behavior, relationships, and multi-object actions into single objects, creating complexity and needless coupling. Many real-world actions operate over sets of entities or relationships between them, which objects model awkwardly. Concept design instead “chunks” behavior around a purpose, encapsulating related actions and relationships across multiple individuals. This separation improves clarity, modularity, and reuse, avoiding the pitfalls of object-oriented domain modeling.*

## Getting confused by object orientation

I've been surprised at how many people think concepts are objects (in the object-oriented sense). And I suspect that I'm largely to blame, for giving names to concepts that sound like objects—*Post* rather than *Posting* for example—and for not being forceful enough in explaining what's wrong with objects.

When I wrote my book, I underestimated how much object orientation has shaped how people think about software. I mistakenly thought I could mostly ignore objects and that it was better just to make the case for a new idea. And I didn't want to pick fights. So I relegated my explanations of why concepts aren't objects to two page-long end notes (on p.246 and p.275) and left it at that. Perhaps I should have guessed that almost nobody would read the end notes, even though many of the key ideas of the book are there (including my favorite pasta recipe!).

As I've seen people use concept design over the last few years, in industry and in my classes at MIT, I've come to see how object orientation leads them astray. I came to realize it was time to be more blunt about why concept design isn't object oriented, to explain how concepts address some of the flaws of objects, and to show what goes wrong if you don't understand these things.

I need to explain first what I mean by 'object oriented.' It's a term with a rich history that has come to mean different things to different people. This piece has turned out to be much longer than I'd planned, in part because we'll need to disentangle several distinct ideas.

## Objects as Individuals

First is the idea that the world can be modeled as a set of 'objects' that have persistent identity. In this view, I'm an object, you're an object, this post is an object, the machine you're reading it on is an object, and so on. Because these objects have identities distinct from their properties, it makes sense to talk about how they change over time. You get older as time passes; the post may be edited; your computer breaks and is repaired. This notion of object is important in software because we need to track these identities (by generating explicit identifiers and ensuring that they point to objects unambiguously) and to have a firm grasp on how representations of objects in code are related to objects in the real world. We need to be clear, for example, about the difference between the object that represents my book *Essence of Software* and the object that represents the particular copy that I just looked at to find those end notes.

This notion of object was known to philosophers long before computing, but it has been popularized by software developers and computer scientists, and we've  contributed to explaining and clarifying it. Jean-Raymond Abrial's 1974 [paper](https://www.academia.edu/143127592/Data_Semantics) entitled *Data Semantics*, which is often cited as a key influence on object orientation, talks explicitly about the problem of mapping identities between the world and software. Peter Chen's invention of [entity relationship diagrams](https://dspace.mit.edu/bitstream/handle/1721.1/47432/entityrelationshx00chen.pdf) in 1976 laid the foundations of modern data modeling, with the idea that the state of the world could be represented as a collection of object sets (called entities) and relationships between them. 

This notion is just as essential to concept design as to any other software development approach. It doesn't require thinking of objects as composite structures that have 'values' distinct from their 'identities.' Better to take the [relational view](https://groups.csail.mit.edu/sdg/pubs/2001/micromodularity.pdf) that an object is represented by nothing more than its identity, and can be associated with other objects and with non-object values by relationships (or mathematically, relations). Thus the fact that Alice and Bob are friends can be represented by a relation called *friend* that includes the pair *(Alice, Bob)*; and the fact that Alice is 25 years old can be represented by a relation *age* that includes the pair *(Alice, 25)*, and there's no fundamental difference between these two cases. 

## Objects as Machines

The second idea is the one embodied by early object-oriented programming languages, first Simula and later Smalltalk. In these languages, an object is a little machine that has its own state and interacts with the outside through 'methods.' A counter, for example, might have a state that is just a number, initialized at zero, and methods *increment*, *reset*, and *display*. By including so-called 'observer methods' you can keep the state secret to the object; to know the current count, you just call *display* and don't need to see the counter variable. 

Implicit in this idea is the assumption that objects are formed into a system by calling each others' methods, or 'sending messages' in Smalltalk lingo. In practice, this means that objects tend to be intertwined with one another, with a method call to one setting of a cascade of method calls through a chain of objects. This introduces undesirable dependencies between objects, because it means that if one method fails (or even just returns an unexpected result) the method that calls it might fail too.

Much of object-oriented programming therefore has been concerned with mitigating these dependencies. The influential Gang of Four [book](https://en.wikipedia.org/wiki/Design_Patterns) catalogued dozens of design patterns, mostly concerned with reducing dependencies, often by introducing elaborate structures. These structures—factories, proxies, observers, etc—are often ingenious and elegant, but they undoubtedly contribute to the complexity and lack of legibility of modern software (more on that in an upcoming post).

This notion of object, like the first, is compatible with concept design, so long as it's used strictly as an implementation technique rather than a domain-modeling principle. Concept design uses action synchronization to eliminate dependencies, which can be seen as a particular implementation pattern (related to the patterns used in many [event-driven architectures](https://en.wikipedia.org/wiki/Event-driven_architecture)) that is readily coded in object-oriented languages.

## Objects as Datatypes

A third notion of object orientation is that sets of objects grouped into classes represent *abstract data types*. Data abstraction emerged in the 1970s and 80s, and is one of the most powerful ideas in programming. The easiest way to understand it is through Barbara Liskov's [observation](https://dl.acm.org/doi/10.1145/800233.807045) that early programming languages provided a collection of built-in datatypes—booleans, numbers, strings, and so on—each with their own operations. Except in low-level languages like C, you couldn't see how the values of these types were represented: an integer was an integer, not a sequence of bits, and if you wanted to know something about its value, you had to use the standard integer operations (for example, equals and greater than). That's good.

Less good is that you couldn't extend the collection of datatypes with a new datatype. For example, you could build dates using records with year, month and day fields, but your dates would look different from built-in types like integers. Liskov argued that the programming language should let you define new types, so you could define a type *Date* that had its own operations and, like the built-in types, could be used without knowledge of its representation.

This idea is orthogonal to the idea of objects. But Liskov aligned the two ideas in her language [CLU](https://publications.csail.mit.edu/lcs/pubs/pdf/MIT-LCS-TR-225.pdf) (1977), resulting in a very elegant programming model. There was no distinction between values and objects; everything was an object, including the integer 3. (In this respect, she was following Smalltalk. Java unfortunately followed C++ in treating primitives and objects differently, creating a mess that despite attempts to patch things up [later](https://docs.oracle.com/javase/8/docs/technotes/guides/language/autoboxing.html) marred the language for ever.)

Other languages, such as [SML](https://homepages.inf.ed.ac.uk/stg/NOTES/node79.html), provided data abstraction without objects. Whereas object-oriented languages (like Java) achieve representation hiding by the simple rule that only the methods of an object can access it, these other languages needed a different trick (notably assigning one type to a value inside a module and a different type outside).

The object-oriented scheme for data abstraction, despite its elegance, has its flaws. Chief amongst these is that methods privilege one object (often called 'self' or 'this') that is the target of the method. This makes it hard to handle operations that involve more than one object. If you define a method for adding two complex numbers, for example, the implementation has to treat them differently, and will let you access the representation only of the first of the two. In a language like SML, you could treat them uniformly. (In fact, you could do that in CLU too, since it adopted a hybrid approach that included both object-oriented structuring and an explicit type discipline to distinguish internal and external views of a data type. As Tony Hoare said about Algol-60, CLU was not only an improvement on its predecessors, but also on nearly all its successors.)

Another flaw, or at least a complication, is that this approach seemed to require that some datatypes be 'mutable.' A set, for example, might have an operation for adding an element. Now things get more complicated. The meaning of equality becomes subtle, since we have to distinguish between two sets that happen to have the same elements right now but are actually different sets. And the hiding of state becomes a liability, because you need to track when two variables (or object fields) are pointing to the same object, and how a call to a method on one object might result in a change in the value of another. Decades later, [separation logic](https://en.wikipedia.org/wiki/Separation_logic) and languages like Rust were developed to cope with these challenges.

In short, data abstraction and object orientation are different ideas, and just happen to have been conflated in the history of programming languages. Whether there's some deep synergy here, as Liskov might have argued, or merely a convenient overloading (in the concept design sense) is beside the point. To understand this topic more deeply, read William Cook's thoughtful analysis in his 1990 [paper](https://dl.acm.org/doi/10.5555/648142.749835) *Object-Oriented Programming Versus Abstract Data Types*.

## Object-oriented Design

Finally we come to the notion of object orientation that is most relevant at the design level, and which I'll argue is misguided and the cause of much trouble.

This notion has its roots in a very compelling idea: that a software system can be constructed in two parts, one a model that tracks the state of the world outside, and the other a collection of queries on this model.

Michael Jackson's [JSD method](https://en.wikipedia.org/wiki/Jackson_system_development) (1986) is the most systematic and elegant formulation of this idea. A banking system, for example, has a model that has an entity for each bank account; as customers perform deposits and withdrawals, the state of the entity is updated; a bank statement is then just a query on the state of the bank account object. Jackson modeled each entity as a long-lived process; this made it possible to handle real-time functionality using the same idea. 

The [Object Management Technique](https://www.amazon.com/Object-Oriented-Modeling-Design-James-Rumbaugh/dp/0136298419) (OMT, 1991) applied this idea in the more conventional context of data modeling and databases, and was one of the sources of UML, a modeling language that has been largely abandoned but lives on in spirit.

Eric Evans's 
[Domain-driven Design](https://www.oreilly.com/library/view/domain-driven-design-tackling/0321125215/) (2003) augmented this idea with a collection of contemporary object-oriented patterns (and introduced more controversially the idea of *bounded context*, that the teams building services that work together should develop their own domain models independently).

So where's the snag? It's in the assumption, which often followed, that the required functionality of the system can be described *purely* (or even primarily) in terms of familiar domain objects. Let's see what goes wrong when you try to do this.

### Functions on more than object

Some functions can be assigned to a single object: an increment of a counter, a deposit in a bank account, or a change of a user's name. In each case, some action that happens in the world corresponds to a method that can be called on a single object, and results in a change to the state of that object (and no other). But many functions aren't like this. A bank transfer, for example, updates the state of two accounts, so it can't be represented by a method on just one. 

When you look closely, it turns out that many actions that seem to concern a single object actually involve the *set* of all objects of its class. Changing a user's name might seem to be about just the one user object, but what if names are to be unique? Then the change can only be allowed if no *other* user has the new name. Or consider password authentication. At first sight, you might think it's about individual users. But think about what happens when users are authenticated. The first step isn't to check the password but to find the user associated with the given user name. After that, we can check whether the password matches the recorded password for that user. Finding which user is associated with a name isn't a method of any user object, but involves querying the *entire set* of users to see which, if any, has that name.

Now of course these functions that act on sets can be represented as methods of objects too, but to do so will require new kinds of collection objects. For user authentication, we'll need an object that represents all the users of the system, for example.

### Relational functions

Things get worse when we consider functions that are about relationships between objects. Most social media functions fall in this category: friending and following relate users to each other; upvoting, bookmarking and favoriting relate users to posts; commenting relates comments to posts; and so on. In any of these cases, trying to assign an action to a method of one object is a fool's errand. Who should *follow* be assigned to, the following or followed user? Is upvoting a method of a user or a post?

One workaround for this problem is to introduce objects to model the relational links themselves: friend objects, upvote objects, bookmark objects, and so on. In data modeling this makes sense and can work well; for one thing, making relational tuples explicit as objects lets you attach other properties to them (for example, the date on which an upvote occurred).

But this doesn't solve the modeling problem if we continue to insist that actions need to belong to objects. Sure you can model friending as the creation of a friend object, but how will you determine whether two users are friends? You'll still need a collection object. And what are you going to call it, by the way? It's not easy to find a compelling name, and that alone should make you suspicious. And if you're tempted to think that you might just augment objects with some built-in collection features, consider how you'd deal with LinkedIn's 'degrees of connection' query that tells you how many friend steps you are away from someone.

### Aggregates

Even if you can assign an action to a single object, it may rely on other objects for its fulfillment. Suppose your online store has a cart object, with an action to add an item to a cart. So far so good. But if you model your cart as containing separate line item objects, now you have a problem: *add* is no longer a method on a single object. Either it involves creating a new line item, which is then added to the cart (say as *cart.add (lineItem)*, or the action is invoked just by calling a cart method (say as *cart.add (itemSku)*). Either way, the two objects (the cart and line item) have become intertwined, and neither can exist without the other.

Domain driven design deals with this problem by defining some objects as 'aggregates' but as we'll see this complication isn't necessary, and arises only because of the insistence that the domain should be modeled with objects whose methods correspond to real-world actions.

### Directionality and dependencies

Two major limitations of object orientation have drawn the attention of researchers, and led to many proposals for new paradigms. One is that relationships between objects must be represented in the instance variables of the objects themselves, and these instance variables prescribe a direction of navigation.

Take the relationship between posts and comments in a social media app. A novice would likely endow the post object with an instance variable that holds an array of comments on that post; this makes it easy to display comments along with a post. But as I note in my book, this is actually *exactly* the wrong way round. Since an app is more likely to have posts without comments than to have comments without posts, the reference should go the other way, with each comment holding the post that it targets. This improves the dependency situation, but now navigating from a post to its comments is no longer possible. In situations like this in many object-oriented codebases, you end up with links in both directions, which makes navigation easy but now introduces an invariant that will need to be maintained.

In short, relationships between objects result in a mass of complications. There have been many proposals to fix this (for example, with links that can be traversed in both directions) but none have been introduced into mainstream languages.

### Separating Views

I've left until last a problem with object orientation that is arguably the most serious, and which has been extensively explored by researchers. It's that object orientation brings all the functionality associated with an object together in one place.

Consider a user object in a typical web app, for example. The conventional way to construct such an object is to ask yourself: what do I know about users? what properties do users have? what actions can be performed on them? This leads to a conflation of all the different aspects of functionality that users are involved in. The very same user object will have instance variables for the user name, password, display name, bio—and maybe even karma points, lists of friends, upvoted posts, and so on.

These all belong to very different functions and should be separated. Friending and upvoting and karma clearly don't belong together, and one can easily imagine apps that have subsets of these, so representing them all together makes an object that is fundamentally not reusable. 

Even if we look at properties that seem to be more basic to users—user name, password, display name, bio—we see conflation. The user name and password are for authentication; the display and bio are for profiles. By separating these, we can describe and even implement these functions entirely separately. My student Eagon Meng points out that even the user name and password can profitably be teased apart, with the user name being relevant only to naming and the password to authenticating that a person presenting themselves is in fact the user that has been named.

Attempts to address this problem go back decades, and include [mixins](https://en.wikipedia.org/wiki/Mixin), [subject-oriented programming](https://en.wikipedia.org/wiki/Subject-oriented_programming), [aspect-oriented programming](https://en.wikipedia.org/wiki/Aspect-oriented_programming) and [roles](https://en.wikipedia.org/wiki/Object-oriented_role_analysis_and_modeling). In gaming, where separating out performance critical functions (such as moving things around) from other functions (such as trading belongings) is vital, a pattern called [entity-component](https://en.wikipedia.org/wiki/Entity_component_system) is widely used. It separates different views of objects and is similar in some ways to our standard implementation of concepts. 

## Chunking with Concepts 

Here's a way to understand what's going on. When we build software, we model three primary kinds of thing: 
- **individuals**, which have persistent identity;
- **relationships** between individuals;
- and **actions** that result in changes in relationships.

For example, in a social media app, the individuals might be users, posts, comments and so on; the relationships are that some users are friends of others, that posts belong to users, that comments are about posts, and so on; and the actions are become a friend, create a post, add a comment, etc. If it helps, you can think of individuals as *nouns*, relationships as *adjectives*, and actions as *verbs*. These provide a vocabulary of behavior, but they don't suggest any structuring principle for how to put behaviors together.

It's well known that in any domain of expertise, what distinguishes experts from novices is their ability to think in terms of larger structures. Psychologists call this [chunking](https://en.wikipedia.org/wiki/Chunking_(psychology)). A musician doesn't think of a composition in terms of individual notes, but in terms of bars, or motifs, or themes; harmony is understood in terms of chords or progressions, and large structures such as canons or fugues. 

In design, patterns (introduced by [Christopher Alexander](https://en.wikipedia.org/wiki/A_Pattern_Language)) can be seen as a way to chunk design elements to give designers a language for talking about design at a higher level.

How should software behavior be chunked? What are the appropriate patterns that will allow software designers to think at a higher level? Concept design proposes a particular kind of pattern, namely the *concept*, that brings together all the behaviors associated with a particular purpose. So, for example, *PasswordAuthentication* is a concept that serves the purpose of authenticating users by storing secret passwords which are set when a user registers and provided again later to establish that the user is the same user who registered before.

In object-oriented design, the *chunks are equated with the individuals themselves*. The economy of this idea is very appealing: why introduce a new notion when an existing one will suffice? (Concept design gurus will feel a twinge of worry here, at the prospect of [overloading of purposes](https://essenceofsoftware.com/tutorials/concept-basics/purpose/).) And indeed the idea works very nicely in some cases. To model a social media group chat (as in WhatsApp, for example) it seems natural to have a *GroupChat* object that corresponds to each group, and whose methods support all the actions that users of the group chat perform: joining and leaving, posting and replying to messages, and so on.

As we've seen, however, this is actually the less common case, and more often the behaviors associated with an object can't be modeled by the object itself. Take password authentication. It would be tempting to define a *Password* object in the hope that it might encapsulate password-related behaviors. But this won't work. Suppose our object contains a user name and a hashed password string. This would allow a method to change a user's password (just update the password string), and a method to check that a presented user name and password match the stored user name and hashed password. But that's only part of the functionality. Given a user name, how will we find the appropriate password object? And what about updating the user name? That can't be allowed if another password object contains the new user name, but that can't be implemented in the scope of a single object. So we'll need some other object, *PasswordTable*, say, that represents a set of user names and passwords. Nothing new here; these are the same issues I outlined above.

This is not an isolated example; it happens everywhere, and it leads to an elaborate collection of interrelated objects where it's no longer clear which object embodies the behavior. With both *PasswordTable* and *Password* objects, we now have password-related behaviors split across multiple objects.

In contrast, in concept design the story is much simpler. All password-related behaviors will be encapsulated in the *PasswordAuthentication* concept, expressed as a collection of actions that take different combinations of arguments (users, user names, passwords, etc). The state of the concept will capture relationships between individuals (objects, if you like) and simple values. Users, for example, will be individuals. Passwords actually won't be, because passwords are just strings, and what makes a particular string a password is that it's the password of some user.

## Bad smells: spotting an object-oriented concept

Emerging concept designers are often confused by object orientation, and they write concepts as if they were class declarations. For example, you might see a concept that looks like this:

**concept** User\
**purpose** manage users\
**state**\
    userid\
    username\
    password\
    email\
    displayname\
**actions**\
    register (username, password)\
        **effects** creates a new user\
    authenticate (username, password)\
        **effects** returns true if the username and password match

The state suspiciously has no sets, and seems instead to list the properties of a single user. But the actions can't be defined on this single user, and their specs sweep this difficulty under the rug. The *authenticate* action makes no sense: which user is to be compared with the given username and password? The presence of the *email* and *displayname* fields conflates user authentication with user profiles; this is why it's not possible to write a coherent purpose for the concept.

Instead the concept should have been written more like this:

**concept** PasswordAuthentication\
**purpose** authenticate users\
**state**\
    a set of Users with\
        a username String\
        a password String\
**actions**\
    register (name: String, pass: String): (user: User)\
        **effects** creates a new user with username name and password pass and returns it\
    authenticate (name: String, pass: String): (user: User)\
        **requires** some user with username name and password pass\
        **effects** returns the matching user\
    authenticate (name: String, pass: String): (error: Error)\
        **requires** no user with username name and password pass\
        **effects** returns an appropriate error message

The scope of the actions is now explicitly a set of users, each with a user name and password. There is no need for a *userid* field; each *User* individual implicitly has an identity. The profile fields have been removed, so the concept is solely about authentication. Profiling can be described in a separate concept.

Concepts are expressive enough to describe an object-oriented structure when needed. Here's a version that lets you group users into different scopes, corresponding to the example mentioned above. Note that there's an action corresponding to a constructor, and all the other actions take the object (the table) as their first argument:

**concept** PasswordTable\
**purpose** authenticate users in different scopes\
**state**\
    a set of Tables with\
        a set of Users\
    a set of Users with\
        a username String\
        a password String\
**actions**\
    new (): (table: Table)\
        **effects** creates a fresh empty table\
    register (table: Table, name: String, pass: String): (user: User)\
        **effects** creates a new user in the given table\
            with username name and password pass and returns it\
    authenticate (table: Table, name: String, pass: String): (user: User)\
        **requires** some user in table with username name and password pass\
        **effects** returns the matching user\
    authenticate (table: Table, name: String, pass: String): (error: Error)\
        **requires** no user in table with username name and password pass\
        **effects** returns an appropriate error message


## Conclusions

Object orientation is a powerful programming mechanism. But the apparent correspondence between objects and individuals in the world is misleading, and modeling the world as a collection of objects turns out to be far from straightforward. Like object orientation, concept design recognizes the identity of distinct individuals, but chunks behavior not around the individuals themselves but around more flexible abstractions that typically contain multiple individuals of different types.