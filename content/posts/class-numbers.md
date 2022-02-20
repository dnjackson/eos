---
author: "Daniel Jackson"
title: "The Class Number Dilemma"
date: "2022-02-24"
description: "A Concept Example in Everyday Life"
ShowToc: false
TocOpen: false
hideMeta: false # removes date etc from post
# hideSummary: true
summary: "Who knew such a simple thing could be so challenging?"
editPost:
    URL: "https://forum.softwareconcepts.io"
    Text: "Comments" # edit text
    appendFilePath: false # to append file path to Edit link
aliases: "/classnumbers/"
---
I'm always excited to find examples outside software that concept design can be applied to. Here's one I came across this week.

As part of a restructuring of our curriculum, we're fixing up our class numbers. Currently, our programming sequence looks like this:

	6.0001 Introduction to CS and Programming using Python
	6.009 Fundamentals of Programming
	6.031 Software Construction
	6.170 Software Studio

After renumbering, it might become:

	6.100 Introduction to CS and Programming using Python
	6.101 Fundamentals of Programming
	6.102 Software Construction
	6.103 Software Studio

Surprisingly, making such a change actually turns out to be *really hard*. One major problem is that (because we have a large department with many classes) this would require reassigning some existing numbers. But MIT's registrar has a rule that you can't reassign a class number unless it has been unused for five years!

What's going on here? The concept of class number has multiple, conflicting purposes: in concept lingo, it's *overloaded*. One purpose is to be a shorthand that signals the role that the class plays in degree requirements. This includes the level of the class (thus hinting that 6.101 would be taken before 6.102) and, in the case of our renumbering, also the general area the class belongs to (thus 6 says it's a class in EECS, and 6.1 that it's in the software track).

The other purpose is that the number acts as a name that persists from term to term (so students and prospective employers can assume that 6.103 is consistent from year to year, for example). It's because of this second purpose that the registrar forbids reassignment of numbers without a five-year gap.

What's the solution to this dilemma? The standard concept design move would be to split into multiple concepts, one for each purpose. We could add a mnemonic name (or commandeer the course title) to serve the naming purpose, and reserve the number for signaling the place in the curriculum. So then we might have, for example:

	6.100 Software Intro
	6.101 Software Fundamentals
	6.102 Software Construction
	6.103 Software Studio

Is this a reasonable solution? I'm not sure. It would require a culture change at MIT to refer to a class by a name ("software studio") rather than a number ("103"). On the other hand, encouraging more mnemonic names might be a good thing and would help beyond the university: I remember that Tim Nelson's class that teaches Alloy at Brown is called "Logic for Systems" and not that its number is "CSCI 1710". And the alternatives are not much better. One ingenious proposal is to satisfy the registrar's rule by appending a zero to each number, and then remove it after five years.

I wonder if there's a better conceptual solution. Let me know if you can think of one!

