---
author: "Daniel Jackson"
title: "The siloing of UX design"
date: "2022-08-26"
description: "Why a new discipline of software design is needed."
ShowToc: true
TocOpen: true
hideMeta: false # removes date etc from post
summary: "There’s no single role for UX design. Instead it’s split amongst roles, with unfortunate consequences."
editPost:
    URL: "https://forum.softwareconcepts.io"
    Text: "Comments" # edit text
    appendFilePath: false # to append file path to Edit link
---
Innovation and software are almost synonyms. It’s often software that's changing the way we live and work (for good and for bad), and new applications of software abound. And then there’s AI which is advancing in leaps and bounds and *might* be a net benefit (but the jury is still out on that).

How strange then that software development, as  a field, is so conservative. We do things pretty much the way we've always done them: mostly by trial-and-error, hacking the code until it sort of works. Agile development brought us more project discipline (in tracking progress, breaking the work into reasonable chunks, etc) and saved us from the often pointless work of writing massive documents that few people read anyway. At the same time, by trumpeting the joy of code it sapped much of the enthusiasm we might have had for thinking carefully about what we were trying to do before writing code (although the advent of design thinking, with its emphasis on need finding, offered a small corrective on that).

## Our favorite 20th century design methods

User-centered design, emerging in the late 1970s and popularized by Don Norman’s book in 1986, brought with it a collection of new practices: need finding (Robert McKim, 1970s); paper prototyping (1980s); user personas (Alan Cooper, 1980s); heuristic evaluation of user interfaces (Jakob Nielsen, 1990); wireframing; and so on.

And as companies began to realize how much the UX of their products mattered, they started to hire visual designers and UX designers.

I don’t think it’s an exaggeration to say that the problem of usability of user interfaces is now solved. Sure, there are still bad UIs around, but most UIs are pretty slick. I rarely look at any product by a major software company and see a serious flaw in the UI. For one thing, flaws don’t last long: if your users are struggling because a button’s mislabeled or in the wrong place, you just fix it.

## Siloing software UX design

Does that mean that we don’t have any more usability problems? Far from it. But, having eliminated the UI flaws, the remaining issues run deeper, in the underlying concepts. As I explain in the opening of [my book](https://essenceofsoftware.com), for example, what confuses people about Dropbox sharing isn’t the UI (which is generally excellent) but the underlying concepts of folder sharing in Dropbox, which have some rather strange properties.

The UX techniques that we have from user-centered design and design thinking are useful and important, but they aren’t sufficient for addressing these issues because they don’t pay enough to attention to the functionality of the software itself.

Design, as Mitchell Kapor [explained](https://hci.stanford.edu/publications/bds/1-kapor.html), is what happens at the meeting point of technology and people. Rather than doing what Kapor called for—namely creating a role for a software designer who would mediate between the two—we’ve instead **siloed** our development teams into those who work on the user side and don’t address the software deeply (UX and visual designers) and those who work on the technology side and don’t address the users (software architects and programmers). Product managers can help bridge the gap, but in many organizations they are relatively junior employees and don’t have the clout or expertise they need to be effective.

## A remedy: concept designers

That’s why organizations need designers who work at the conceptual level, with a deep understanding of both users and their needs and the software concepts that are built to support them. 

Back to the Dropbox example. Such a person would want to understand what users need from a file sharing app; how people work together, and how they organize their own “digital gardens”; what risks they face regarding security and privacy; and so on. But just as importantly, such a person would want to understand deeply the existing concepts used for file sharing, from Unix folders to file synchronization, and concepts for controlling access, from access control lists to capabilities and authentication concepts.

Instead, what we do today is to divide the design work between two siloed groups. The requirements analysts and UX designers create the outlines of the product, and the programmers fill in the details. But, as the Eames’s famously put it, the details aren’t just details: they *are* the design. So with this approach you end up with concepts that have rough edges and don’t have the power, flexibility and robustness that they might have had.

## Understanding software, not code
Now perhaps you’re thinking this is impossible: nobody could have the expertise of both a UX designer and a programmer. But that’s missing the point. You can understand software concepts *without* being a programmer.

To know what a hard link in Unix is (and how it can break the operational principle of the Trash concept) requires only a **conceptual understanding**; you don’t need to be able to read the Unix source code. 

What concepts offer is a way to talk about software functionality in a precise and sophisticated way that is implementation independent.

Going back to Dropbox and file sharing: to be a good UX designer, you’ll need to understand folder hierarchies, path names, aliases and links, etc., but the breaking of files into storage blocks can be completely ignored. That’s the software engineer’s responsibility: if it’s done right, the user need not ever know it’s happening. But the user can’t ignore the distinction between soft and hard links, because they *behave* completely differently.

One final example: as a UX designer, you shouldn’t need to know anything about distributed algorithms. It’s the implementer’s job to make sure that data is moved around and replicated in an appropriate way. But notions of *consistency* and *asynchrony* are another matter: if your programmers insist that eventual consistency is good enough, you’ll need to be able to figure out the impact on users.

## Where to start? A new educational strategy

How then can we break down the UX design siloes and create designers with the right combination of skills? 

Within companies today, UX designers, product managers and programmers can work more closely together. UX designers can expand their training in software concepts; and product managers and programmers can learn more about social, psychological and ethical factors.

But the future will be impacted most, I believe, by a new kind of software design education, in which students are taught how to work at Kapor’s interface between human and machine: combining the technical aspects of software with an appreciation of human needs and purposes.

## A new software design class

These are the principles that have guided the development of a new class that I’m teaching with [Arvind Satyanarayan](https://arvindsatya.com) at MIT. In Software Studio (6.1040*), we teach students how to think about software design through the lenses of [concept design](https://essenceofsoftware.com) and [value-sensitive design](https://vsdesign.org). 

In our class, students learn (and practice!) a collection of standard user-centered design techniques, including needfinding, brainstorming, sketching and wire framing, paper prototypes, user testing and heuristic evaluation of UIs. But they also learn how to formulate and analyze designs precisely in terms of concepts, and how to explore the social and ethical impacts of their work.

These ideas are not taught in the abstract, but through concrete examples. Students apply them first in weekly individual assignments (in which they design and built Fritter, a Twitter clone that addresses design problems they identify in Twitter itself), and then in a major team project, in which they build an app of their own choosing.

It remains to be seen how successful this will be, but the evidence of growing enrollment is encouraging. The class began as a combination of a traditional programming class (in which we taught functional programming in JavaScript, Node.js, etc.) and a design class. Over time, we’ve increased the design component and reduced the programming component—aided by the adoption of TypeScript as the language of our prerequisite programming class.

We were initially concerned that this move towards design would damage the appeal of the class. But in fact the opposite has been true, and the class has grown dramatically from year to year. This fall we will have our largest enrollment ever, and we’re excited to see how it will go. MIT students, it seems, aren’t only interested in learning how to hack code for that summer job. They want to design software that will change the world, and to do that, they realize that paying some attention to the world matters too.

\* *Our class number was previously 6.170. How the number changed, and how we ended up with 5 digits, is itself an [interesting story](https://essenceofsoftware.com/posts/class-numbers/) in conceptual design.*