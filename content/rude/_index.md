---
title: “Rude”
layout: "home"
url: “/rude”
summary: “rude”
ShowBreadCrumbs: false
---

## Concepts are new? Really?

It’s true that even Plato talked about concepts (even if he didn’t build software). Conceptual models became a hot topic in the 1980s, and it’s hardly new to say that concepts play a role in software design. What’s been missing, though, is any workable notion of what a concept *actually is*. My book is the first to explain *what concepts are* and show that they can be defined in a modular way.

## Why does any of this matter?
Because if you can describe concepts and put them together, you can think about software in a new way, and make it more flexible, robust and easy to use. If you’re not convinced that this way of thinking is helpful, here’s a challenge: take any of the problems with mainstream apps described in the book and find a simpler way to explain it. 

## Modularity is new? Why not objects?
Modularity is an old idea. And we have lots of kinds of modules in programming (ADTs, objects/classes, closures, actors, etc). But none of these can tease apart the different concepts in a software app (at least I couldn’t do it!). Objects might seem promising, but they don’t work (see Note 81 in the book). 

## Is modularity such a big deal?
Yes, because without it there’s no real reuse. And there’s no separation of concerns: you can’t talk about one concept without getting muddled up in another. Also, if you want to implement your concepts as fully separate code modules, you’ll need modularity.

## Why are you so nitpicky?

I do admit (at the very start of the book) to being a “micromaniac”. I’m fascinated by small things and how they can have big impacts. But remember that in software small flaws have a habit of leading to big disasters. Is it nitpicky to point to flaws in a cloud drive that can cause users to lose their life’s work?

## Won’t LLM’s make this all irrelevant?
On the contrary, concepts can provide exactly the modularity that LLMs need to build full apps incrementally. We’re working on this...

## Why isn’t your book easy to read?
Sorry if you find it challenging, but I hope it’s still fun. It has more examples of real design issues in major products than any other book I’ve read, and that alone makes it harder. And there are some Easter eggs in there (such as a typographically infinite page).