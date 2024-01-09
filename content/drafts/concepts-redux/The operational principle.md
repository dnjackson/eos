# The operational principle

Using a term coined by Michael Polanyi (and brought to software engineering by Michael Jackson), I call a scenario that explains how a product “just works” its *operational principle* (OP).

The operational principle is just a sample scenario, but it includes enough richness to convey typical usages. The example I gave above for Zoom could be written in a more textual way like this

	<create, start, join+, end>

where *join+* means one or more *join* actions. Another scenario would be this

	<create, start, end>

in which the meeting is started but nobody joins, so the poor host ends it without talking to anyone. That’s a perfectly *valid* scenario, but it’s not *useful*, so it wouldn’t count as an OP.

Another example: To use a traditional elevator, you call it (by pressing the up or down button), you enter the first elevator that arrives, select your floor, and exit when you arrive:

	<call, enter, select, exit>

Some modern elevators work differently: you select the floor in the elevator lobby, enter the elevator at the indicated bank, and then exit at your floor:

	<select, enter, exit>

Note that we could enrich the OP to make it clear that you have to enter the correct elevator, writing something like

	<select(f):b, enter(b), exit(f)>

to say that when you select floor f, the system responds with elevator bank b, and you then enter an elevator in bank b, and exit at floor f. Likewise we could have indicated that the participants in our Zoom meeting use the link it was started with:

	<create():m, start(m), join(m)+, end(m)>

A restaurant reservation system lets you reserve a table, then start using it at the prescribed time, and then end using it some time later:

	<reserve, start, end>

(How much later the end action might occur is a difficult design challenge: if diners stay too long, the restaurant fails to deliver on its OP, but if the reservation slot is longer than necessary, tables will sit empty.)

The OP of a software app represents its innovative essence. Some OPs were invented just for software (or for computerized systems)---the elevator’s, for example. Many, like the restaurant reservation OP, were invented as a social protocol that existed long before computers (back in the 19th century, in fact). And some, like Zoom’s, are a kind of hybrid: naming a meeting place in advance is not so different from what people have always done when they say “let’s meet at the pub at 7pm” (although, unlike the pub, Zoom’s meeting place is created on demand from an unlimited pool).

The non-obviousness of an OP may be a symptom of cleverness: the elevator that begins with floor selection, for example, allows better load balancing (by directing travelers to particular elevators).

But a trivial OP can be a significant innovation too. Take the OP for blogging platforms, which is little more than this:

	<post, read+>

That is, the blogger posts an article and then multiple people read it. This seems almost too silly to specify. But it’s more subtle than it seems. The action of reading an article involves specifying 

how is selection specified???

someone performs a read, they 

 read actions are  read 

 actually relies on a 


 the converse doesn’t hold: a trivial 

it’s a mistake to think that a trivial OP is evidence of no ad

don’t be the converse is not true

iPod


	pay, park, leave

all these OPs have interesting subtleties
if a one space zone, don’t need car license (but they may still ask so they can prevent you sharing?)

If you don’t believe that use is part of the system, see what happens if you make lots of OpenTable reservations and don’t turn up.

OPs are often computerizations of social protocols. parking, reservations, calls, meetings in places.


Can describe socio-technical systems too.
The OP for social security:

	<assign, contribute+, retire, withdraw+>

>as a simple sequence 

Comparisons
drinking band to simple age verification
Can be very simple, novelty is in comparison to more complex one. Social media posting.



This was the argument that Apple’s detractors used 
This argument has become weaker over the years as Apple’s detractors (who claimed that design didn’t matter and that Apple’s fancy products would never compete against the cost effectiv
