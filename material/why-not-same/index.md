---
author: "Daniel Jackson"
title: "Why not try something new?"
date: "2022-08-29"
description: "How UX design is currently siloed and why change is hard."
ShowToc: true
TocOpen: true
hideMeta: false # removes date etc from post
summary: "How UX design is currently siloed and why change is hard."
editPost:
    URL: "https://forum.softwareconcepts.io"
    Text: "Comments" # edit text
    appendFilePath: false # to append file path to Edit link
---
Innovation and software are almost synonyms. It’s often software that's changing the way we live and work (for good and for bad), and new applications of software abound. And then there’s AI which is advancing in leaps and bounds and *might* be a net benefit (but the jury is still out on that).

How strange then that software development, as  a field, is so conservative. We do things pretty much the way we've always done them: mostly by trial-and-error, hacking the code until it sort of works. Agile development brought us more project discipline (in tracking progress, breaking the work into reasonable chunks, etc) and saved us from the often pointless work of writing massive documents that few people read anyway. At the same time, by trumpeting the joy of code it sapped much of the enthusiasm we might have had for thinking carefully about what we were trying to do before writing code (although the advent of design thinking, with its emphasis on need finding, offered a small corrective on that).

## Our favorite 20th century design methods

User-centered design, emerging in the late 1970s and popularized by Don Norman’s wonderful book in 1986, brought with it a collection of new practices: need finding (Robert McKim, 1970s); paper prototyping (1980s); user personas (Alan Cooper, 1980s); heuristic evaluation of user interfaces (Jakob Nielsen, 1990); wireframing; and so on.

And as companies began to realize how much the UX of their products mattered, they started to hire visual designers and UX designers.

I don’t think it’s an exaggeration to say that the problem of usability of user interfaces is now solved. Sure, there are still bad UIs around, but most UIs are pretty slick. I rarely look at any product by a major software company and see a serious flaw in the UI. For one thing, flaws don’t last long: if your users are struggling because a button’s mislabeled or in the wrong place, you just fix it.

## Siloing software UX design

Does that mean that we don’t have any more usability problems? Far from it. But, having eliminated the UI flaws, the remaining issues run deeper, in the underlying concepts. As I explain in the opening of [my book](https://essenceofsoftware.com), for example, what confuses people about Dropbox sharing isn’t the UI (which is generally excellent) but the underlying concepts of folder sharing in Dropbox, which have some rather strange properties.

The UX techniques that we have from user-centered design and design thinking are useful and important, but they aren’t sufficient for addressing these issues because they don’t pay enough to attention to the functionality of the software itself.

Design, as Mitchell Kapor [explained](https://hci.stanford.edu/publications/bds/1-kapor.html), is what happens at the meeting point of technology and people. Rather than doing what Kapor called for—namely creating a role for a software designer who would mediate between the two—we’ve instead **siloed** our development teams into those who work on the user side and don’t address the software deeply (UX and visual designers) and those who work on the technology side and don’t address the users (software architects and programmers). Product managers can help bridge the gap, but in many organizations they are relatively junior employees and don’t have the clout or expertise they need to be effective.

## A remedy: concept designers

That’s why organizations need designers who work at the conceptual level, with a deep understanding of both users and their needs and the software concepts that are built to support them. 

Back to the Dropbox example. Such a person would want to understand what users need from a file sharing app; how people work together, and how they organize their own “digital gardens”; what risks they face regarding security and privacy; and so on. But additionally, such a person would want to understand deeply the existing concepts used for file sharing, from Unix folders to file synchronization, and concepts for controlling access, from access control lists to capabilities and authentication concepts.

Instead, what we do today is to divide the design work between two siloed groups. The requirements analysts and UX designers create the outlines of the product, and the programmers fill in the details. But, as the Eames’s famously put it, the details aren’t just details: they *are* the design. So with this approach you end up with concepts that have rough edges and don’t have the power, flexibility and robustness that they might have had.

## Understanding software, not code
Now perhaps you’re thinking this is impossible: nobody could have the expertise of both a UX designer and a programmer. But that’s missing the point. You can understand software concepts *without* being a programmer.

To know what a hard link in Unix is (and how it can break the operational principle of the Trash concept) requires only a **conceptual understanding**; you don’t need to be able to read the Unix source code. 

What concepts offer is a way to talk about software functionality in a precise and sophisticated way that is implementation independent.

## A reason change is slow: risk-averse students
Here’s one way that practices in industry change over time. Researchers in universities spend their time thinking about new ways to do things; they teach these new ways not only to their graduate students but to their undergraduates too; then those students go into industry and become agents of change.

Consider, for example, how functional programming, which used to be regarded as an academic obscurity, has become totally mainstream. This happened in large part because researchers such as Hal Abelson and Gerry Sussman taught Scheme; Matthias Felleisen and his students Shriram Krishnamurthi, Robby Findler, Matthew Flat and others taught their version of Scheme and then Racket; Bob Harper taught SML; Simon Peyton Jones taught Haskell; and so on. And many of these people wrote remarkable books (such as [SICP](https://en.wikipedia.org/wiki/Structure_and_Interpretation_of_Computer_Programs) and [HTDP](https://en.wikipedia.org/wiki/How_to_Design_Programs)) that were read not just by students but also by practitioners.

But over the last decade or so, there’s been a worrying trend. Our students are more focused than ever on getting jobs. They want to spend their time in university learning how things have been done in industry in the past, not how they’ll be done in the future.

I’m not sure where this is coming from. Perhaps it’s from companies, who have always wanted universities to reduce their training costs. As Mary Shaw [notes](https://dl.acm.org/doi/pdf/10.1145/336512.336592), this goes back to the 1960s when employers’ top priority was for students to learn “more [JCL](https://en.wikipedia.org/wiki/Job_Control_Language).”

Perhaps it comes from our lack of confidence and vision as faculty, as we jettison courses full of powerful ideas (such as SICP) and instead switch to teaching how to program in Python like every bootcamp on the planet.

Certainly our students often don’t seem to understand that the purpose of their education is to give them skills and sensibilities of lifelong value, and that a university has different goals from a trade school. They seem to feel that their role is not so much to shape the future of the companies they join but to fit in. I’m loath to blame the students though, because it’s our job to inspire them and help them understand what an education can be.
