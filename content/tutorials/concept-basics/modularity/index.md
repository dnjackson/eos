---
author: "Daniel Jackson"
title: "Concept modularity"
weight: 10
date: "2022-09-22"
description: ""
ShowToc: false
TocOpen: true
hideMeta: false # removes date etc from post
summary: ""
editPost:
    URL: "https://forum.essenceofsoftware.com"
    Text: "Comments" # edit text
    appendFilePath: false # to append file path to Edit link
---
## Why modularity matters

To see why modularity is so critical in software design, consider what goes wrong when it’s missing. 

For the programmer, life becomes miserable. Whenever you need to understand or modify some piece of functionality, there’s no single place in the code to go to, and you may have to visit many widely separated locations (and just finding them can be a major challenge). Even removing (let alone adding) functionality is hard, because the parts of the codebase are all interconnected in subtle ways, and taking anything away risks breaking the parts that remain.

For the user, things are bad too. It’s possible that the code has no modularity but that, viewed from the user’s perspective, the app is perfectly modular. But this is unlikely, and poor modularity in the code is usually reflects a lack of structure in the app’s functionality. Consequently, users have trouble understanding how the software works, because the individual functions aren’t separable. Everything is interconnected, and to understand one part you have to understand another, and then another...

## Modularity: the holy grail of software

Not surprisingly, then, achieving modularity has been a central goal of programming practice, and a primary motivation in software engineering and programming language research for decades.

We’ve invented a host of new abstractions (functions, closures, abstract types, objects/classes, streams, etc), new programming paradigms (functional, object-oriented, [aspect-oriented](https://en.wikipedia.org/wiki/Aspect-oriented_programming), [subject-oriented](https://en.wikipedia.org/wiki/Subject-oriented_programming), [role-oriented](https://en.wikipedia.org/wiki/Role-oriented_programming), [process-oriented](https://en.wikipedia.org/wiki/Process-oriented_programming), [reactive](https://en.wikipedia.org/wiki/Reactive_programming), [prototype-based](https://en.wikipedia.org/wiki/Prototype-based_programming), [actor-based](https://en.wikipedia.org/wiki/Actor_model), ...), principles ([information hiding](https://en.wikipedia.org/wiki/Information_hiding), [representation independence](https://web.mit.edu/6.031/www/sp17/classes/12-abstract-data-types), the [Liskov substitution principle](https://en.wikipedia.org/wiki/Liskov_substitution_principle), the [Law of Demeter](https://en.wikipedia.org/wiki/Law_of_Demeter)), and patterns (for [object-oriented code](https://www.oreilly.com/library/view/design-patterns-elements/0201633612/), [enterprise architectures](https://www.oreilly.com/library/view/patterns-of-enterprise/0321127420/) and more)---all of which are motivated by improvements in modularity.

And it’s not just in software; modularity may be the [most valuable asset](https://direct.mit.edu/books/book/1856/Design-RulesThe-Power-of-Modularity) in design.

## Where concept design comes in

The new idea in concept design is that modularity is born not in the code, but in the *function* of the software. If you can structure an app’s functionality in a modular way, then you can carry over that modularity to the code.

And because the function is modular, users will be able to understand it in a modular way. This is essential for usability, because it lets users learn how to use an app in pieces incrementally, and reuse their knowledge across systems.

A concept is a bundling of functionality that meets three modularity criteria:
- *Specificity*: The concept fulfills a specific [purpose](https://essenceofsoftware.com/tutorials/concept-basics/purpose/) that brings real value to the user, and does not mix, multiple separable purposes.
- *Completeness*: The concept’s functionality meets its purpose fully.
- *Independence*: The concept stands by itself, without reference to other concepts.

Let’s now look at each of these criteria in turn, see why they’re needed, how they might be violated, and what you have to do make them hold.

## Specificity

Having a specific purpose includes having enough function to bring real value, but not so much that separable purposes are being mixed.

For example, consider a *User* concept whose purpose is to provide authentication of users. Almost all web-based apps include this concept. Suppose the concept just included registration, in which a user creates an identity by providing a username and a password.

This might be regarded as a reasonable increment of functionality in some approaches. As a [user story](https://en.wikipedia.org/wiki/User_story), it might be expressed like this: “As a customer, I can create register as a user so that I can go shopping.” 

But in fact registering brings no real value to the user, because without the functionality associated with the subsequent authentication check, registration does nothing useful. The value that the user receives (which is more about other users bring prevented from acting on their behalf) is provided by the authentication mechanism in its entirety. So the purpose must be user authentication, and not user registration alone.

This would be apparent if you tried to write an [operational principle](https://essenceofsoftware.com/tutorials/concept-basics/principle/):

	concept User
	purpose
	  help users register for services
	principle
	  after a user registers... ???
	actions
	  register (name, password: String, out u: User)

There’s no way to complete the scenario because there aren’t any other actions to include! And when you start trying to figure out what action you might add, you’ll see that the one you need is the authentication checking action.

You might be tempted to expand the purpose to include all user-specific settings. For example, perhaps each user has a display name, and an avatar. These are not relevant to the purpose of authentication, however; they serve a different purpose (letting users choose how to present their identities to others), and should be part of a different concept.

Ensuring that a concept has a single, specific purpose makes it more understandable. If *User* included the avatar feature, you might start wondering whether that plays a role in authentication somehow. And it makes it more reusable by unbundling features: you might want authentication without avatars, for example.

In practice, formulating a suitably narrow purpose can be tricky, and often the easiest way to do it is to consider which particular features would be included in the concept’s functionality. Then, as you realize that certain features should be included (both registering and checking) and that others should be excluded (display names and avatars), you can refine your purpose accordingly (in this case, that the purpose is simply authentication and nothing more).

## Completeness

Completeness just means that the concept includes all the functionality needed to fulfill the purpose. So if the purpose of *User* is authentication, then offering registering without checking is obviously insufficient.

A concept may fail to be complete for more subtle reasons. Suppose our *User* concept supported checking by offering an action that, given a user id, username and password, returns an indication of whether or not the username and password are correct for that user. This design fails to fulfill the purpose completely, because when the check is applied, the identity of the purported user isn't known! Instead, the concept must include a lookup, for example by having the action take the username and password and return the user identity (if a matching one exists).

This example might seem silly and pedantic, but it illustrates a key difference between concepts and classes in object-oriented languages. In an object-oriented implementation of user authentication, you might have a *User* class with each instance holding a username and password. The functionality I just mentioned would be exactly what such a class provides, and it wouldn’t support the lookup. For that, you’d need to add a static component to the class that maps usernames to *User* objects. Using static state isn’t really in the spirit of object-oriented programming through, so you might instead create a *Registry* class each instance of which holds such a mapping. Thus a single concept becomes several classes that are tightly coupled together.

Finally, it’s worth noting that completeness only means that the purpose is *minimally* satisfied. There are always additional features that might be desirable; for example, it would be useful for users to be able to change their passwords (and even their usernames).

Being complete ensures that all the functionality associated with the concept’s purpose is in one place, encapsulated within the concept.

## Independence

A concept not only has to meet its purpose completely, but it has to do it without the help of other concepts.

Recognizing that storing and checking passwords involves some non-trivial functionality (notably encrypting and salting), you might think to put it in its own concept.

That would be mistake, however. First, encrypting and salting passwords is not user-facing functionality, so it doesn’t make a good concept. Second, suppose you did factor it out, say into a Password concept, assuming the *User* concept could delegate some functionality to it. Now your *User* concept would fail the independence test, since it would require the Password concept to operate.

Concepts have no [dependencies](https://essenceofsoftware.com/tutorials/concept-basics/dependency/) of this sort, and have to self-contained. As a result a concept can’t fail because of a bug in another concept, and functionality is truly localized.

You may wonder whether this rule undermines some modularity in the code. It doesn’t, because nothing prevents you from implementing password salting and encryption in a separate module, which can be one of several modules realizing the concept.

Concept implementations will also inevitably depend on libraries for general services (such as string manipulation, arithmetic, file management, etc). So concept structuring won’t spare you entirely from the problem that fixing a bug in a function may require looking in multiple places. For example, your password encryption might fail because of a bug in a mathematical library. 

But, in contrast to conventional design approaches, there won’t be pieces of connected functionality at the same *level* in scattered locations (for example, passwords being encrypted in one place and decrypted in another, far away).

## Genericity

A concept may be functionally independent of other concepts while still mentioning them in its design. 

Here, for example is a very rudimentary *Email* concept:

	concept Email
	  purpose exchange of messages
	  principle
	    after a user sends a message to another user,
	    they can receive that message
	  state
	    from, to: Msg -> one User
	    body: Msg -> one String
	  actions
	    send (from, to: User, body: String, out m: Msg)
	    recv (u: User, m: Msg)

This is obviously very simplified; the state is a global set of messages each of which is associated with the users it’s from and to, and some body. A more realistic description might include state components for holding sent and received messages, and an explicit system action for transferring messages from one to the other.

Here’s another concept, commonly used in mail clients:

	concept Label
	  purpose organize and filter messages
	  principle after adding a label to a message m, 
      if you get messages with that label, the message m will be included in the results
	  state
	    labels: Msg -> set String
	  actions
	    addLabel (msg: Msg, l: String)
	    // return messages that have all labels in ls
	    getMessages (ls: set String, out msgs: set Msg)

Again, this is obviously simplified: the concept would usually support rich queries over label combinations.

Notice that the *Label* concept appears to depend on the *Email* concept, because it mentions the *Msg* type that it generates. But if you think about the behavior of *Label*, since it involves only the *identity* of the messages (and not their content), the fact that the labeled items are messages is completely irrelevant. It matters only in understanding the role that the concept plays in the application as a whole.

So to make this clear and improve the concept, we can eliminate the reference to messages, and make it generic over some unspecified *Item* type:

	concept Label [Item]
	  purpose organize and filter items
	  principle after adding a label to an item i, 
      if you get items with that label, the item i will be included in the results
	  state
	    labels: Item -> set String
	  actions
	    addLabel (i: Item, l: String)
	    // return items that have all labels in ls
	    getItems (ls: set String, out items: set Item)

This is much better because it makes it clear that *Label* is a more general concept that allows the organization and filtering of any kind of item. 

More subtly, our *Email* concept has the same problem, because it refers to the type *User*, which presumably is the type of user objects generated by our *User* concept. But again, the concept does not depend on the user objects having any particular properties, so we can make the *User* type a parameter:

	concept Email [User]

In this case, we could keep the references to “users” in the description of the concept, but if we wanted to be really pedantic we could replace it by a name (such as *Principal* or *Account*) that makes it clearer that a human user might not be involved.

With all these generic concepts in hand, to describe our application we need to instantiate the type parameters:

	app EmailClient
	includes
	  User
	  Email [User.User]
	  Label [Email.Msg]
	
This says that the type of users in the *Email* concept will be the *User* type from the User concept, and the type of the targets in the *Label* concept will be the *Msg* type from the *Email* concept.

## Permutation invariant: for experts

We can make precise this idea that a type is generic with the notion of *permutation invariance*. Suppose there is some execution of the concept comprising a sequence of actions interleaved with the resulting states. Now imagine taking the elements of the set in a given type, for example all the user objects in the set *User*, and permuting them, swapping *u0* and *u1*, swapping *u2* and *u5*, and so on. If you applied this permutation to the execution, would the result also be a valid execution? This is just a fancy way of saying that all that matters is the *identity* of the objects. If the answer is yes, then this permuted type is being treated generically.

This reasoning will tell you that the *Msg* type in *Label* and the *User* type in *Email* are generic. But it will also tell you that the *String* type in both concepts is generic! That’s because the concepts don’t do any string-specific operations: they just store the strings. So the *Email* concept, for example, could be written even more generically like this

	concept Email [User, Content]
	  purpose exchange of messages
	  principle
	    after a user sends a message to another user,
	    they can receive that message
	  state
	    from, to: Msg -> one User
	    body: Msg -> one Content
	  actions
	    send (from, to: User, body: Content, out m: Msg)
	    recv (u: User, m: Msg)

making it clear that the concept works with any kind of content. For email this might seem a bit artificial, since email messages are always textual, But for a social media post concept the genericity is more important (since a post’s body might be text, image or a combination), and factoring out the content into specialized concepts (such as a concept for creating and formatting rich text) would be helpful.

## Wow, this is hard!

This may seem pretty challenging to you. And you’d be right. Designing modular concepts is not at all easy. But that’s because design is hard, and it’s even harder when you’re trying to be modular.

With practice, you’ll develop this skill. You’ll see immediately when a concept isn’t modular and what you need to do to fix it. And you’ll begin to think naturally in terms of modular concepts.

And it’s worth the effort! Your designs will be more understandable, more focused and more ... well, modular. When you come to implement them, you’ll be able to translate them very directly into code and you’ll be unlikely to hit structural obstacles. And you’ll be spared all the refactoring that is often needed when programmers build an app before they’ve really understood what they’re trying to do.
