---
author: "Daniel Jackson"
title: "Upvote: An Example Concept"
date: "2021-11-17"
description: "An example of what a concept catalog entry might look like"
tags: ["upvote", "catalog", "concept"]
# categories: ["themes", "syntax"]
# series: ["Themes Guide"]
ShowToc: true
TocOpen: true
hideMeta: false # removes date etc from post
summary: "An example of a concept catalog entry that defines a concept's 
purpose, behavior and design issues."
editPost:
    URL: "https://forum.softwareconcepts.io/t/upvote-sample-concept-catalog-entry/32"
    Text: "Comments" # edit text
    appendFilePath: false # to append file path to Edit link
aliases:
    - /posts/upvote/
---
Concepts provide a framework for recording design experience. Designers often find themselves reinventing the wheel, designing and deploying concepts that have been built many times before, and then rediscovering a raft of subtle problems that were already known.

My dream is to have a concept catalog (or a collection of concept catalogs, perhaps organized by domains) that would hold a collection of concept definitions, and with each the accumulated experience and design wisdom from previous deployments of that concept.

As an example of what such a catalog entry might look like, here is a sample entry for the Upvote concept. Comments, suggestions and reactions welcome in the [forum topic](https://forum.softwareconcepts.io/t/upvote-sample-concept-catalog-entry/32) for this example.

This idea is inspired, of course, by the success of design patterns in programming.

**Name:** Upvote [Item, User]  

**Purpose:** Track relative popularity of items  

**Also known as:** Like  

**Related concepts:**  
Reaction (send emotional reaction to author of post)  
Recommendation (track user approvals for future recommendations)  
Moderate (maintain quality of forum by approving submissions)  
Karma (grant privileges to users based on good behavior)  
Flag (crowdsource moderation by having users mark bad items)

**State:**  
```
upvotes, downvotes: Item -> set User  
count: Item -> one Int  
// item's count is defined as number of ups minus downs  
// all i: Item | i.count = #i.upvotes - #i.downvotes
```

**Actions:**  
```
 upvote (i: Item, u: User)   
  u not in i.upvotes
  i.upvotes += u
  i.downvotes -= u

downvote (i: Item, u: User)   
  u not in i.downvotes
  i.downvotes += u
  i.upvotes -= u

unvote (i: Item, u: User)   
  u in i.(upvotes+downvotes)
  i.upvotes -= u
  i.downvotes -= u 
```

**Operational principle:**
```
after a sequence of upvote and downvote actions,  
the count of an item is equal to the number of users  
who upvoted minus the number who downvoted
```

**General notes** 
 
***Syntax***. Since this is the first concept that I'm adding to the
catalog, here are a few notes on the syntax I'm using. This is the structure I use in [EOS](https://essenceofsoftware.com), but with more formal definitions of the actions. I'm using [Alloy](alloytools.org)-like syntax for expressions and statements, which is hopefully intuitive even to those unfamiliar with Alloy. The state and actions part of the concept model is not new to concept design; it's just a definition of a state machine activated by atomic actions as you'd find in any formal modeling language (such as TLA+, B, Z, VDM, OCL).

***Declarations***. The declaration `r: A -> B` declares a relation from `A` to `B`, constrained by any multiplicities (using the keywords `one` for exactly one, `lone` for zero or one, `some` for one or more, and `set` for zero or more). 

***Action definitions***. Action definitions can include constraints on the pre-state, as well as update statements. For example, in `upvote`, the first line is a precondition saying that you can't upvote an item if you've already upvoted it. An update statement changes a relation, using +/- for adding and removing tuples. For example, in `upvote`, the second line says that the set of users associated with the item `i` in the `upvotes` relation is increased by `u` (or, equivalently, the tuple `i->u` is added to the relation).

***Type parameters***. The list of types after the name of the concept gives the type parameters. These are types that are polymorphic, and can be bound to other types when the concept is composed. In this case, all the types are parameters. The `Item` type, for example, may be bound to `Comment` or `Post` in another concept, and the `User` type may be bound to any kind of principal (as the term is used in the security community).

***Operational principle***. The OP is given informally. As explained in EOS, it's an archetypal scenario, and unlike use cases, doesn't need to cover all the functionality (so note that unvote is not mentioned). In this case, it looks a bit pathological because the concept is pretty simple: it basically just counts votes.

**Design issues**  

***Negative count***. This design allows counts to go negative. An alternative is to make a minimum of zero (even if the number of downvotes is still tracked implicitly).

***Unvote as own action***. In some deployments, you may use `downvote` to let a user reverse their `upvote` action instead of `unvote`, but this won't work if downvoting is limited (eg, by Karma) to certain users (as in Hacker News).

***Using count***. The count of an item can be used in different ways. Often it is shown to users, and is used to determine the rank order in which items appear. Sometimes the count is used directly for item ordering (as in comments in the NY Times), but more complex schemes are common: in Hacker News, for example, the rank of a post is computed from the number of points, the post's age, and other factors.

***Preventing double voting***. This design prevents a user from voting twice on a single item. In practice, this requires either tracking the identity of the user (eg, with session tokens), or by using a proxy for the user, such as the IP address, MAC address or browser id.

***Visibility***. Usually only the count relation (and not the upvotes and downvotes relations) is made visible, so users cannot see who voted for an item. For Facebook "likes", usernames are visible, but that can be seen as an artifact of combining the Upvote concept with the Reaction concept.

***Eventual consistency***. Getting exact counts is generally not required, so common implementations scale by not requiring strong consistency. This means that a user who upvotes an item will generally see that item's count increase, but may not see the effect of a simultaneous (or even earlier) upvote from another user until later.

***Known uses***. Universally used in all social media apps, typically for posts and comments. Facebook combines the Upvote, Reaction and Recommendation concepts into a single "Like" concept, with some confusing outcomes (for example, that an angry reactions counts as an upvote).

***Synchronizations***. Commonly synchronized with authoring concepts (such as Comment and Post), and with Karma (where an upvote may be synchronized with a reward action, so that a user needs to obtain some number of upvotes before reaching a certain karma level).
