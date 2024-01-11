---
author: "Daniel Jackson"
title: "Software = concepts"
weight: 210
date: "2022-08-23"
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
Any software app, service or  system can be viewed as a **collection of interacting concepts**.

## Concepts are the building blocks of software

Imagine explaining Twitter to someone who's never used it. You might tell them about the *Tweet* and *Follower* concepts, then maybe *Hashtag* and *Like* (aka Upvote), and if they're keen to know even more, you could explain *VerifiedAccount* or *Bookmark*. 

Each concept can be explained with a purpose (what it's for) and an operational principle (how you use it):
- **Follower**. *Purpose*: let you see content from preferred users. *OP*: if you follow someone, then their content will appear in your feed.
- **VerifiedAccount**. *Purpose*: prevent impersonators. *OP*: if someone verifies their identity to Twitter's satisfaction (eg, by showing a link to it on their official website), then their account gets marked with a blue tick that tells people it's real.

I picked the word *concept* because its informal meaning is close to what I want to express. Technical usages of the term “concept” have meant specific and different things. A concept in software is something richer than a concept in concept lattices or conceptual models (where it usually means a class, so that “dog” refers to the class of all dogs, for example). A software concept has a rich behavior that serves a purpose. You can define the full behavior of a concept with a state machine, by declaring a state space (aka a data model) and some actions that read and write states.

## Concepts are software genes

Concepts are like a kind of genetic code. Just as the behavior of an organism is determined by its genes, so the behavior of an app is determined by its concepts.

And like genes, most concepts are common across apps. All social media apps have *Friend* or *Follower* (or both); any app with content from many users has *Upvote*; almost all apps have *Password* or something similar for user authentication; and so on. This is why it's usually easy to learn a new app: you know almost all the concepts already.

And like genes, concepts can mutate. Twitter's *Tweet*, Facebook's *StatusUpdate* and WhatsApp's *Message* are all mutants of a basic  *Post* concept.

## Concepts are life patterns

Most concepts in software apps are just software equivalents of real world concepts: patterns of behavior that humans have enacted for centuries.

Concepts such as *Post* (sharing content with the public) or *Message* (conveying content to one or more specified recipients) are communication patterns that have been around for millennia, ever since people carved words into stone tablets. *Folder* and *Label* reflect traditional ways to organize things: partitioning into groups, or attaching visible classification marks.

## Concepts are technological inventions

But even the most well established life patterns were invented, by some person at some time. Restaurant reservations? Late 1800s. Social security accounts? 1930s. Using passports to identify people when traveling to foreign countries? Apparently Henry V in 1400s England. Publishing content? From short after the advent of writing, I’d guess.

Not surprisingly, people keep inventing new concepts. Some are life patterns first and only become software concepts when apps or systems are built to support them. Most banking concepts are like this. 

Some concepts don’t mirror a pattern in daily life, or do it only weakly (and so we think of these as “metaphors”). Photoshop’s *Layer* concept is a tiny bit like an artist’s acetate layer, but it’s so much richer and more powerful that it counts as an invention in its own right.

I suspect there are also concepts that appear for the first time in software and then make their way into daily life. Let me know if you can think of one!

## What’s new about this?

Perhaps you’re wondering: aren’t these concepts you’re talking about just what people call “features”?

The fundamental difference is that concepts, unlike features, can be defined independently. A concept is free standing, and can be reused between one app and another. That’s what makes concepts understandable: when you learning how to use HackerNews and you discover that is has an *Upvote* concept, you know what it’s for and how to use it, because you’ve seen the same concept in other apps (for upvoting comments in your newspaper, for example).

Features are generally inseparable from the app they belong to, and their effect is often defined as an extension of modification of some existing behavior. This means that features are much more general than concepts, and almost any step in the development of a program can be viewed as adding or removing a feature. But this generality means that features provide less leverage for thinking about design, because they can’t be designed and analyzed independently.

## What are concepts most like?

Some people see a connection between concepts and object-oriented classes, but that seems confusing to me. Classes are a very code-oriented notion, and a class (traditionally) defines a collection of objects. A concept typically involves multiple collections of objects and relationships between them: for *Follower*, there are the users who follow each other and the posts they publish.

A better analogy is that a concept is like a tiny service—a microservice, but even smaller. You could call it a *nanoservice*. Like a service, and unlike a class, a concept provides value by itself, and interacts directly with the outside world.

## How do concepts fit together?

Concepts are independent only in the sense that they can be *understood* independently. They can’t *execute* fully independently of each other or they wouldn’t need to be part of the same app.

In a social media app, the *Friend* concept will ensure that users can view content published by their friends; and the *Post* and *Comment* concepts will govern how users create posts and add comments. But these can’t execute independently, because the comments will have to be added (by *Comment*) to posts (from *Post*), and both comments and posts will have to be treated as published content (by *Friend*).

This is what concept synchronization is for: a way to compose concepts so that they interact together without requiring one concept to be defined in terms of another.

## Tips

Ways to use these ideas:

- Inventory the concepts of an app to understand it and identify its strengths and weaknesses.
- Define a product family by the concepts that its members share.
- Point to “differentiator” concepts (like Photoshop’s *Layer*) that give a product an advantage over its competitors.
- When designing an app, steal existing (and familiar) concepts before inventing new ones.
- Whenever possible, align concepts with life patterns.