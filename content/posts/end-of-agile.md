---
author: "Daniel Jackson"
title: "The End of Agile"
date: "2023-07-18"
description: "Why agile no longer makes sense in the LLM era."
ShowToc: false
TocOpen: false
hideMeta: false # removes date etc from post
summary: "Why agile no longer makes sense in the LLM era."
editPost:
    URL: "https://forum.softwareconcepts.io"
    Text: "Comments" # edit text
    appendFilePath: false # to append file path to Edit link
---
# Feeling like Cinderella
To celebrate my lab’s 60th birthday, we put on a two-day bash: one day of talks [from alumni](https://www.csail.mit.edu/CSAIL_20_60) and one day or talks [from the faculty](https://www.imaginationinaction.co). I started my talk by noting that the title of the event was “AI Frontiers & Implications”, and that I felt like Cinderella, being a plain old computer scientist gatecrashing an AI ball.

Our lab’s name ([CSAIL](http://csail.mit.edu)) stands for “Computer Science and Artificial Intelligence Laboratory,” but of course nowadays it’s all about AI. Like many of us, I’ve sometimes wondered if AI will make everything I’ve worked on irrelevant. After all, won’t GPT-N (for some sufficiently large N) be able to design and code all our software without human intervention?

# Why AI will never make a great programmer

For now, I’ve come to the conclusion that there’s no risk of this in the near future. I noted in another [recent talk](http://people.csail.mit.edu/dnj/talks/index.html#alliances-2023) that there are three predictions you should never make: (3) that the stock market will crash this year; (2) that a certain politician is too stupid and corrupt to win election; and (1) that GPT won’t ever be able to do X (for any X). Nevertheless, despite all of GPT’s amazing capabilities, there’s good reason for skepticism:

- LLMs like GPT aren’t reliable. They make stuff up, and they make mistakes. There’s no evidence that this will ever change.
- GPT is really good at writing code that’s like all the code that’s been written before. Nobody’s shown any examples of it inventing new and better ways to write code, so if we just leave it to GPT-4, our code will be stuck with the knowledge that StackOverflow had in 2021, when GPT-4’s training ended. And if we stop innovating, a future version of GPT won’t know any more. I think of this as a kind of [vigilance decrement](https://en.wikipedia.org/wiki/Vigilance_(psychology)#:~:text=Vigilance%20decrement%20is%20defined%20as,detect%20a%20weak%20target%20signal.) writ large: just as a human driver can’t take over when a self-driving car gets in a pickle, so we human coders won’t be able to fix a massive software mess that LLMs create if we don’t continue to hone our skills.
- Sometimes it’s OK if software mostly works. But this is less common than you might imagine, and software has a habit of becoming critical the more useful it is. We just won’t tolerate software that has occasional but devastating failures.

# Are LLMs like compilers?

Some people say LLMs will be like compilers. Initially, there were skeptical programmers who believed they could write better machine code themselves. But eventually people realized that compilers saved so much trouble that it was rarely worth the effort of writing low level code yourself.

Are the LLM naysayers like those early compiler skeptics? LLMs are already successfully generating complex code fragments, and many programmers rely on them daily. 

But this analogy misses a crucial point. The reason that high-level languages took over is that compilers are completely dependable. Nothing is ambiguous in the instructions a compiler is given. So we can define correctness for a compiler and, given enough resources, even verify it.

What’s so great about LLMs as coders, in contrast, is that they take a vague specification and fill in the implicit details from the context. This is what makes  using an LLM to generate code so compelling: you just nudge a bit, offer some hints, and if you’re lucky, you get what you wanted. The downside is that there’s no guarantee that the ambiguities will be resolved the way you expected.

# An impressive example of LLM coding

Here’s an example of a coding task that I gave ChatGPT last week that illustrates its amazing ability to fill in the gaps in a vague specification.

My dad has recorded our family tree in a massive text file. I wanted GPT to generate a program to read this file and draw the tree. I started by telling it what problem I was trying to solve, giving it just one entry from the file and asking it to write a function to parse it.

Here’s what my entry looks like:

	0001 born 09-01-1964 London
	0001 male
	0001 marr 01 0014 01-02-1989 NYC USA
	0001 name Daniel Nicholas Jackson
	0001 fthr 0002
	0001 mthr 0003
	0001 chld 01 0482 0014
	0001 chld 02 0505 0014
	0001 chld 03 0515 0014
	0001 note 01 Computer Scientist, CMU & MIT
	0001 note 02 Oriel College, Oxford, physics
	0001 note 03 MIT MS, PhD Computer Science

I wasn’t surprised that GPT was able to write the parsing code. What blew me away was that it correctly interpreted every single field in this structure even though I’d told it nothing about the encoding. It inferred without any help that the line

	0001 chld 02 0505 0014

meant that the person with ID 0001 has a second child with ID number 0505 who was born also to the person with ID 0014 (matching the ID of the person 0001 is married to, as shown on an earlier line).

I then gave GPT a series of additional instructions: to extend this function across the whole file; to handle some new fields it hadn’t seen before; and to generate a graph in DOT format. All this worked fine and the resulting 100 or so lines of code had no bugs, as far as I could tell.

# Domain knowledge is critical

My takeaway from the family tree experiment is that domain knowledge is GPT’s secret sauce. It’s indeed impressive that it can write routine code (such as a basic parser) so effectively, but its ability to fill in the gaps in a spec is even more remarkable.

To illustrate this, let me show you one more example. I was playing around with having GPT generate code for some basic concepts. I wanted some code for a *Friend* concept, so I started (mischievously) by giving it a rather abstract specification:

> Can you write a JavaScript class for me? I want it to maintain a graph between nodes, and associate a set of items with each node. It should include (1) a method that connects a node n1 to a node n2, (2) a method for associating an item i  with a node n, and (3) a boolean method that takes a node n and an item i and returns true if the node is connected to a node associated with the item.

My intent was that the nodes would represent users, and the items would represent posts. So this would embody the key operational principle of *Friend*: that you can see your friends’ posts.

The code GPT generated in response had a basic flaw: it made the node relation asymmetric, so if I friended you, it meant I could read your posts but you couldn’t read mine. This asymmetry resulted from my asking for “a method connects a node n1 to a node n2”; if I said instead “a method for connecting one node to another”, it correctly implemented a symmetric relation.

So what if instead I revealed the concept?

> Can you write me a Javascript class to implement the friend concept in a social media app? It should track who is friends with who, and which posts someone has written, and then allow access to a post to a user if it was written by one of the user's friends. There should be (1) a method to make one user a friend of another, (2) a method to record that a user wrote a post, and (3) a method that returns true if a user can access a post.

The code GPT produced for this prompt was much better. What was most exciting was that it not only made inferences from my spec (eg, that the friend relation should be symmetric) but it actually *violated* my spec in a desirable way. Look at this method:

	class SocialMediaApp {
	...
	  canAccessPost(username, postId) {
	    if (this.users.has(username) && this.posts.has(postId)) {
	      const postAuthor = this.posts.get(postId);
	      if (postAuthor === username) {
	        return true; // User can access their own post
	      }
	      const user = this.users.get(username);
	      return user.friends.has(postAuthor);
	    }
	    return false;
	  }
	}

GPT has correctly inserted a special case: that even though you’re not friends with yourself, you should be able to read your own posts.

# The future of programming: unremarkable observations

What are the implications of all this for the future of programming? First, some unremarkable observations that others have made many times before:
- GPT is amazing at writing routine code. It can save you tons of time and relieve the tedium of writing basic functions.
- GPT is a great interlocutor for discussing technical questions (what platform or API to use, what algorithmic challenges you might face, etc).
- GPT writes competent, standard code and can’t be expected to do anything that requires true creativity (like a new design pattern).
- GPT isn’t like a compiler: it makes mistakes, so code review is more important than ever.

These facts alone mean that the way you program (and teach programming) will change a lot. I’m particularly intrigued by the new role that code review will play, and how classic ideas about invariants and verification might become more relevant than ever.

# The end of agile?

I’m most excited by the ways in which GPT differs from all software engineering tools we’ve previously encountered.

When you use GPT as a programmer, you can spend much less time writing code (and tests), because GPT can do that for you. Instead, you spend your time writing prompts (aka specifications), and creating an overall structure for the code that GPT is writing.

What does agile development tell you to do? Roughly, to put all your effort into coding, and to spend almost no time on specification. Thinking hard about overall structure is derided as “big design upfront” (BDUF) and we’re told that we can always “refactor” later.

LLMs have turned all this on its head. Now the focus of our work is writing thoughtful “documentation”: prompts that we give to GPT that hold our specs and all the background knowledge we’d like it to exploit. Coding matters less than ever before.

# Granular domain knowledge and concepts

My two little experiments suggest that GPT’s coding ability is dramatically amplified if it has appropriate domain knowledge. In these cases, the domains (family trees and social media) were presumably well represented in GPT’s training set.

But what about less familiar domains? What if GPT hadn’t known about friending? How will GPT exploit domain knowledge in a novel application? 

Here’s where I believe concepts have a vital role to play. Concepts embody domain knowledge in a granular and reusable fashion. If we had a repository of concepts, we could pick the concepts that are relevant to a particular coding task and give them to GPT as prompts.

My intuition is that GPT already has a large number of concepts represented implicitly in its training set. I’m excited to explore what would happen if we made concepts explicit. Perhaps GPT could fill in subtle aspects of a specification more effectively if it had a fuller representation of the concept at hand. And perhaps it could replicate the kind of “common sense exception” that we saw (in the Friend example, for reading your own post) in much more novel and unfamiliar domains.

# Design still matters

My family tree story actually had a disappointing ending. Sadly, I wasn’t able to draw a nice tree. It was so big and had so many crossing edges that it was impossible to read. I tried to improve the layout by having GPT write code to assign each person to a generation, and then to layer the output so that each generation occupied one horizontal layer.

When prompted, GPT did helpfully point out some of the challenges in assigning generations (eg, that the notion isn’t even well defined if you have a disconnected subtree). But it became clear that I’d hit a design problem that GPT at least wasn’t able to solve without much more prompting.

Might a rich set of concepts for family trees have given GPT the knowledge it needed to complete this task? Maybe. But at the very least this example suggests that even apparently simple problems can harbor subtle design challenges, and for now, software designers are still needed.
