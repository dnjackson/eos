---
author: "Daniel Jackson"
weight: 230
title: "Concept purposes"
date: "2023-09-11"
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
## The role of purposes

**From what to why**. The beginning of wisdom for a designer   is to stop asking “what?” and start asking “why?”. 

This applies at every level. You can ask “what application shall I design?”. But a better question is “why would I design that?”. And at a finer grain, the question “what features should my app have?” gives way to “why should my app have these particular features?”.

**Granular justifications**. Software designs are always taught to think about the purpose of the product as a whole: who it serves, and what value it delivers. This is an essential part of needfinding (as it’s called in the HCI community) or requirements analysis (as it’s called by software engineers).

But they’re rarely taught to justify the *elements* of their designs. Instead, a designer will often assemble an elaborate array of functions and will ask only whether the totality of the design meets the users’ needs. No case is made for each individual element; it’s included just because it’s part of the overall design. The result of this is a lot of functionality that is poorly targeted, or worse, unnecessary.

The alternative is that each element of functionality gets its own justification. But what’s an element? Do we have to explicitly justify every line of code?

**Concept purposes**. In concept design, each concept is given a justification called a *purpose*. You might sometimes find it helpful to record justifications for smaller elements (such as the actions of a concept), but in practice that’s too much work, and justifying entire concepts is good enough.

**A rationale for existing**. A concept purpose is an explanation of why the concept exists; it’s the rationale for going to the trouble of designing it, or when the concept already exists, the rationale for including it. This is critical input to the designer’s decision of whether including the concept is worth the cost (in design, implementation, user interface complexity, etc).

**Knowing why**. The concept purpose is useful to the user too. Knowing why  a piece of functionality exists is usually the first thing a user needs to understand, before they even consider how to use it.
- *Example: Melania Trump’s mistake*. In my [book](https://essenceofsoftware.com) I tell the story of how Melania Trump “favorited” a tweet that made fun of her husband. She presumably thought that this would save the tweet for her privately; unfortunately, her action made public that she had “liked” the tweet. Her mistake was understandable, because Twitter’s explanation of the *Favorite* concept didn’t explain what it was *for*, so she confused what was essentially an *Upvote* concept for a *Bookmark* concept (which in fact was missing, and added to Twitter later).

## Some examples of purposes

Articulating the purpose of a concept can be hard, but the effort pays off in deeper understanding and making the design work that follows more effective (because you know what you’re really trying to do).

Here are some examples of purposes for familiar concepts:
- *Trash*. The purpose of the *Trash* concept, introduced originally in the Apple Macintosh in 1984, is not to make deletion easy. On the contrary, its purpose is to allow *undeletion*.
- *Style*. The purpose of the *Style* concept, widely used in all desktop publishing tools and word processors, is not to let you change the typographic settings of a piece of text; the *Format* concept is sufficient for that. Its purpose is to help you keep a document consistent by changing the formatting of a whole collection of paragraphs (eg, all the headings) in one go.
- *Notification*. The purpose of the *Notification* concept in social media apps is to draw your attention to activity that may be of interest. At least, that was the original purpose, and that’s the purpose that most users would like it to have. Unfortunately, in many cases its purpose seems to be to increase user engagement by drawing users back to the app even when there is nothing of value for them there.
- *AuthenticationToken*. This purpose of this concept, used for example by OAuth and many other schemes, is to allow users to grant access to some resource in some service (for example, the contacts in your Gmail account) to different parties. The idea is that each party gets a different token, so the user can revoke access for one party without revoking it for another.

## Criteria for good purposes

What makes a good purpose definition for a concept? Here are some criteria:
- **Need-focused**. The purpose should be stated in terms of the needs of the user. For example, the purpose of the *Upvote* concept should not be to “express your approval of an item” since that has no tangible benefit; a better purpose might be to “use crowd-sourced approval to rank items.”
- **Specific**. The purpose should be specific to the design of the concept at hand. For example, even though an *Autocomplete* concept in Gmail may make the app more attractive to consumers, it wouldn’t  be useful to say that its purpose is to “increase the Gmail user base” since presumably all concepts have that goal. A better purpose might be “to save the user typing effort.”
- **Evaluable**. A purpose should be a yardstick against which to measure a concept design. For example, a good purpose for the *Trash* concept is “to allow undeletion.” The apparently similar purpose “to prevent accidental deletion” is not good because evaluating the concept design against that goal would require all kinds of assumptions about user behavior.

## Concept specificity

Obviously, a concept should have at least one purpose. If it has no purpose, there’s no reason for it to exist. 

More controversially, a concept should have *at most* one purpose. This might seem strange, especially since some people think that a good design decision solves multiple problems at once (or feeds many birds with one scone, as the politically correct version of the old adage goes).

In fact, this is a misconception, and rigorously separating purposes so that each concept has only one purpose is the path to more flexible software. The chapter on specificity in my book has many examples of design flaws that arose because a concept was given more than one purpose.
- *Example: Facebook’s Reaction concept.* In Facebook, the *Reaction* concept is used both to register approval (for deciding which posts to include in users’ feeds) and to send an emotional reaction back to a post’s author. Users are especially confused by the fact that clicking on the *angry* emoticon is a *positive* upvote for a post. The entangling of purposes means that it’s not possible for a user to do one with the other: you can’t approve a post, for example, without conveying a reaction back to the author, or vice versa.
- *Example: Facebook’s Friend concept*. In Facebook, the *Friend* concept serves two purposes. One is to provide access control so you can limit who sees your posts. The other is to filter your own incoming content, so you can choose whose posts you want to see. (Arguably the design of the *Feed* concept has undermined this second purpose, but that’s another matter.) These two purposes are not always in alignment; you might want to see someone’s posts but not share yours with them, or vice versa.

Avoiding having more than one purpose for a design component is an idea that appears in many different domains. Programming advice often recommends that each function should do only one thing (and David Parnas identifies combining purposes in a single function as one of the main causes of a program not being amenable to [extension or contraction](../dependency)). In mechanical engineering, ensuring that each design parameter corresponds to at most one functional requirement is one of the “axioms” of [axiomatic design](https://en.wikipedia.org/wiki/Axiomatic_design).
