---
author: "Daniel Jackson"
title: "Operational principles"
weight: 3
date: "2022-08-02"
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

A compelling way to explain how something works is to tell a story. Not any story, but a kind of defining story that shows, through a typical scenario, why the thing is useful and fulfills its purpose.

The Minuteman Library Network, for example, offers a wonderful service. If I request a book, then when it becomes available at my local library, I get an email notifying me that it’s ready to be picked up.

Note the form this scenario takes: *if* you perform some actions, *then* some result occurs that fulfills a useful purpose. Many kinds of mechanism can be described in this way:

- If you make social security payments every month while you work, then you will receive a basic income from the government after you retire.
- If you insert a slice of bread into the toaster and press down the lever, then a few minutes later the lever will pop up and your bread will be toast.
- If you become someone’s friend and they then publish an item, you will be able to view it.

One reason that scenarios are so compelling is that we observe them all around us. Using an elevator is actually quite complicated, but we learned how to do it by watching other people. Many people haven’t yet learned how to use [Schindler’s PORT elevator](https://www.youtube.com/watch?v=Q8aaz3NTvgg), so the story needs to be told explicitly:

- Enter the floor you want to go to on the keypad and note the elevator number that is subsequently displayed; now wait for the elevator with that number, and it will stop at your floor.

Each concept has one or more such stories that explain how to use the concept, and show that the concept fulfills its purpose. I call such a story an **operational principle** (OP), a term introduced by the chemist philosopher Michael Polanyi and made known to the software world by my father Michael Jackson.

## Use cases, OPs and snooze alarms

Because an OP is a scenario, you might confuse it with a use case. But these are very different things. Use cases are for specifying the requirements of a software system, so even a simple system will have a large number of use cases, covering not only ones in which the user succeeds but also those in which the user fails. 

In contrast, the OPs of a concept aren’t intended to specify it fully, so they can be few and brief. The OPs characterize a concept’s essential behavior, explaining how it works and fulfills its intended purpose. All details that are not fundamental to the design are left unspecified. 

Consider a digital snooze alarm clock. The first OP is this:

- If you set the alarm for a given time, and turn the alarm on, then when that time comes around, the alarm will ring (for some fixed time or until you turn it off).

We can add a second OP to explain the snooze function:

- When the alarm starts ringing, you can press the snooze button, and it will stop ringing and start ringing again after some fixed time, and you can then turn it off.

These OPs tell you enough to use the alarm clock, and they justify its design. But they barely begin to answer all the questions you might have about the details of its behavior. What happens if you press the snooze button again? If you turn the alarm off after pressing the snooze button but before the alarm rings again?

Of course the designer will have to answer all these questions in a reasonable way, but they’re not the essence of the design. That doesn’t mean that we won’t come across additional scenarios that matter enough to be recorded as OPs. This one, for example:

- If you set the alarm for a given time, do any sequence of actions except for setting the time, and then turn the alarm on, then it will ring when the given time comes around.

This ensures that when you set the alarm time it “sticks” from day to day, and snoozing (in particular) won’t change it. It’s not the only possible design. You could imagine  a clock that resets the alarm time to 8:05am if it was set previously to 8:00am and then snoozed.

## Specifying full behavior

So if the OP doesn’t fully specify the behavior of a concept, how do you do that? I’ll cover that in detail in another post, but here’s the idea. In short, you define the state of the concept and then define the effect of each action on the state (just like writing high-level code):

	state
	  alarmTime, ringTime, now: Time
	  on: Bool
	  ringing: Bool = on && 0 < (ringTime - now) < RINGT

	actions	
	  setAlarmTime (t: Time)
	    alarmTime := t
	  tick (t: Time)
	    now := t
	  alarmOn ()
	    on := true; ringTime := alarmTime
	  alarmOff ()
	    on := false;
	  snooze ()
	    if ringing
	      ringTime := ringTime + SNOOZET

**Notes**. The alarm is defined to ring for a time RINGT after it goes off and to snooze for a time SNOOZET. The passage of time is modeled explicitly by an action that updates the current time.

**Exercises for the reader**. (1) How must SNOOZET be related to RINGT, and why? (2) What happens if you set the alarm time to just before the current time?
 
If what you’re trying to do is fully specify a concept’s behavior, this kind of state machine model is the way to go. It’s much more succinct than scenarios (and so better than use cases, in my view, in this role).

But OPs offer something else. In a technical sense, you can infer all scenarios from the state machine, but that doesn’t mean you can make sense of them. To understand the snooze function you need to know how it’s typically used: the alarm rings, you press snooze and then it rings again a bit later. 

So the OP helps by distinguishing the archetypal scenario from other scenarios that are equally plausible (in terms of the state machine) but play no role in explaining the concept’s purpose. For example, you can let the alarm ring until *just* before the moment at which it switches off and press snooze then. This scenario is as valid (to the state machine) as our first OP (in which you press snooze when it starts ringing) but it’s not helpful in understanding why the snooze function was invented.

## How many OPs?

A simple concept may have just one OP. Here’s an example of a concept whose OP is almost too trivial to explain:

- **Comment**. If you add a comment to an item, then the comment will appear in the list of comments displayed with it.

Some concepts, though, need two or more OPs to explain their essential functionality:

- **Trash**. (1) If you delete a file, you can then restore it by moving it out of the trash. (2) If you delete a file and then empty the trash, the file is gone forever and its storage space can be reused.
- **Password**. (1) If you register with a user name and password, and then you login with that same user name and password, you will be authenticated as the user who registered (that is, able to act in their name). (2) If you register with a username and password, and then you login with that same username but a different password, you will not be authenticated.
- **Personal access token (PAT)**. (1) If you create an access token for a resource and pass it to another user, then that user can enter the token string and obtain access. (2) If you create an access token, pass it to another user, and then revoke it, the other user will not be able to access the resource with it.

It’s not unusual for a more elaborate OP to be the one that actually motivates the concept design. For the *PAT* concept, it’s the revocation that’s the essence: without it, a simple password would do.

## Generic and context-specific OPs
Most concepts are generic, which means that they can be instantiated in different contexts, applying to different kinds of objects. The *Trash* concept, for example, can be applied to files and folders in a file system; or to messages in an email client; or to photos in a catalog. 

When writing an OP as part of a concept design, you’ll have to decide whether to write it in a generic form, or instantiated for the context at hand. My recommendation is that you write it initially in its instantiated, more concrete form, as this will let you assess more easily whether the concept will provide the value that’s expected. Then, once you’re happy that the concept is the right one, you can reformulate it in a more generic way to gain the benefit of a simpler and more flexible concept, and to ensure that you haven’t specialized the concept in some way that will make it unfamiliar. 

Here’s an example. Suppose you’re designing an email client, and realize that it would be convenient if the app were to automatically complete email addresses when they are typed in to the *to* or *cc* fields. You might start with an OP like this:

- **PreviousRecipientCompletion**. After you have sent emails to a variety of addresses, each time you send an email and start to type an address, the app automatically suggests a completion based on the addresses previously used.

Having written this, you realize there is nothing email-specific about this; this is just a simplified form of a generic concept:

- **Autocompletion**. When you start typing a string, the system suggests suffixes that would extend the string to one that appears in a predefined dictionary, or to one that you previously typed.

## Purposes, not user goals

The design of a concept is driven by some purpose that the concept fulfills. In some cases, this purpose is aligned with a simple user goal. The purpose of the *TextMessage* concept is to convey messages between users; a user following the OP (if you send a message to another user, then they will receive it) has the simple goal of conveying a single message.

In general, however, user goals are not so easily aligned with purposes. A user always has some *motivation* to perform some action or sequence of actions, but the motivation is often inchoate and not worth dwelling on in the design process.

The purpose of the *Password* concept, for example, is to authenticate users. Neither the user who successfully logs in nor the user who fails has a goal that fully matches this purpose, whose essence is to *distinguish* the two cases.

One of the risks of focusing on user goals is that it becomes tempting to articulate shorter scenarios that have no relationship to the purpose at all. There’s no point writing a scenario for registering a password, for example. It provides no value in itself to the user, and matters only in the context of the OP in which it’s followed by a successful (or unsuccessful) login.

## OPs with multiple users

Another reason an OP might have no user goal is that the purpose involves collaboration of multiple users. The purpose of *Upvote* is to let users rank items by their popularity. When you as a user give a thumbs-up to an item, you’re not fulfilling a goal worth articulating, but simply contributing to a larger communal purpose. The OP for *Upvote* must include upvotes by multiple users on multiple items, to illustrate the key idea that the ranking of items depends on the number of upvotes they receive.

## The danger of “secondary” goals

One final problem with goals: they encourage you to think that some goals are primary and others secondary. But it may well be the supposedly secondary goal that motivates the design of the concept.

Take the *ShoppingCart* concept, for example. You might be tempted to label as primary the OP in which a user adds an item and checks out immediately (thus buying the item in the most efficient way), and label as secondary the OPs in which the user adds multiple items, or adds and then removes items. 

But just as the purpose of the *Trash* concept is not deletion but *undeletion*, which is illustrated by the OP in which an item is deleted and then restored, so the purpose of *ShoppingCart* is to allow items to be selected for purchase without commitment. If it were merely to make it easy to buy items, a different, simpler concept would suffice (such as Amazon’s one-click *BuyNow*).

## Writing good OPs

OPs offer a lightweight and provocative way to explore a new design or record an existing one. Here are some tips on writing good OPs.

- **Purpose**. Have the concept purpose in mind: that the purpose of *Trash* is undeletion (and not deletion), or that the purpose of *Password* is authentication (and not creating accounts or anything like that).
- **Full history**. Remember to include a long enough history of actions to demonstrate the purpose. Registering a password is useless by itself; it only becomes interesting when you log in later.
- **One concept**. If your OP looks complicated, you may be mixing the OPs of distinct concepts. A scenario in which a user logs in, views a friend’s post and then comments on it involves at least three concepts (*Password*, *Friend*, *Comment*).
- **Actions not buttons**. Write the OP in terms of the actions and states that form the concept (and the user’s mental model), not the realization of the concept in the user interface. So keep out details of UI buttons and views, and avoid especially breaking a single action (such as a login attempt) into multiple steps (enter name, enter password, press submit).
