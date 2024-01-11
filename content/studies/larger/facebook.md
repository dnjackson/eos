---
author: "Daniel Jackson"
title: "Facebook and Dark Patterns"
date: "2022-03-25"
description: "Finding Clarity in Design"
ShowToc: false
TocOpen: false
hideMeta: false # removes date etc from post
# hideSummary: true
summary: "What's really going on with dark patterns?"
editPost:
    URL: "https://forum.essenceofsoftware.com"
    Text: "Comments" # edit text
    appendFilePath: false # to append file path to Edit link
aliases:
    - /posts/facebook/
---

[Dark patterns](https://uxdesign.cc/dark-patterns-in-ux-design-7009a83b233c) are no longer just yucky strategies that companies get away with, tempting you to buy insurance you don't need or sign up for a free-for-a-while trial that can only be canceled by calling a phone number that is always busy. Soon they might be illegal. [Regulators](https://www.ftc.gov/news-events/events/2021/04/bringing-dark-patterns-light-ftc-workshop) are taking a closer look, and lawyers in the [US](https://www.law.com/legaltechnews/2021/11/03/dark-patterns-are-an-ethical-and-legal-no-no-but-is-your-legal-team-prepared-for-them/?slreturn=20220223153700) and the [UK](https://www.farrer.co.uk/news-and-insights/dark-patterns-an-overview-of-legal-considerations/) are starting to warn about the liabilities they bring.

But what exactly makes a pattern dark? Where is the line between an aggressive but legitimate sales tactic and an immoral or illegal scheme?

# A new project

In a research project funded by a new [NSF program](https://beta.nsf.gov/funding/opportunities/designing-accountable-software-systems-dass) about designing accountable software systems, I'm working with my students [Geoffrey Litt](https://www.geoffreylitt.com) and [Josh Pollock](https://joshmpollock.com) (and my co-PIs [Danny Weitzner](https://internetpolicy.mit.edu/daniel-weitzner/) and [Joan Feigenbaum](http://www.cs.yale.edu/homes/jf/) and their students) on answering this question.

Our initial take involves—surprise!—software concepts. To date, dark patterns work has focused on user interface tricks: getting you to click the big green button, for example, when it's really that small text field that doesn't even look like a button that you want to click instead. But the more serious dark patterns run deeper, and are conceptual in nature. 

# As bad as it gets

For example, in one of the most egregious scams I've ever seen (thanks so Eunsuk Kang for telling me about this), Intuit advertises a version of the Turbotax tax filing app as "guaranteed free." In fact, however, as Propublica has [documented](https://www.propublica.org/article/turbotax-just-tricked-you-into-paying-to-file-your-taxes), the app is far from free, and if you fail to fall within a narrow range of cases, you will be required to pay (after, of course, you've taken all the trouble to enter your private and personal tax data). Intuit and other tax software companies had lobbied to prevent the IRS from offering its own free solution (as the revenue agency does in many other countries), and in return promised to provide one itself. The free one, it turns out, is not called the "Free Edition," but rather the "Freedom Edition," and is not reachable from the Turbotax website.

# Dark patterns in Facebook

Most cases of dark patterns are more subtle. Take Facebook, for example. Suppose Alice posts a photo of Bob, and Carol (a friend of them both) then tags Bob in the photo. This seemingly innocent action has an unexpected and pernicious consequence: it makes the photo visible to all of Bob's friends, even those who are not friends of Alice or Carol. Perhaps the photo shows Bob at a job fair talking to a competitor and his friends include his work colleagues. There are all kinds of reasons that Bob might not want the photo to be shared in this way.

Now Facebook would likely respond that its users should know how the app behaves, and that they are free to choose more stringent privacy settings. Bob could have turned on a "tag review" setting that would have required his approval before the photo was shown to all his friends. In practice, of course, most users don't have a clue what's going on, or how to change their settings. And not surprisingly, the defaults seem to favor Facebook rather than its users.

(I've written a piece for a lay audience about how Facebook uses "dark concepts" to further its sometimes nefarious business practices: you can find it [here](https://press.princeton.edu/ideas/facebooks-dark-design-its-not-just-the-algorithms).)

# The key idea

Here's our idea. What's wrong with simple UI dark patterns is that they *confound the expectations* of the user: you think that big green button is the normal approval of your purchase, but it turns out to be the one that signs you up with extra insurance that you didn't want to buy. Patterns that operate more deeply also confound expectations, not about the meaning of user interface widgets, but about the very behavior of the concept at hand. When you see a familiar concept like a shopping cart, you bring with you all your prior experiences of shopping carts, so you assume this shopping cart will be no different. You don't expect items to be added to the cart spontaneously without your approval; shopping carts just don't do that.

The use of a common concept thus establishes a kind of implicit contract between the company and the consumer. In exactly the same way, if an employee of a supermarket came and dropped items into your cart because they "thought you might like them," your grounds for objection should not be that such an action is inherently evil. It's that shopping carts just don't work that way.

# Facebook's concepts

Let's apply this idea to Facebook. Our task is to come up with plausible concept definitions that capture our expectations of activities such as posting, friending and tagging. This will not be easy because apps like Facebook are so baroque that arguably our expectations are uncertain and inconsistent. So instead we'll try to imagine how an app like Facebook *might* work if it were designed to be as simple and clear as possible.

Our first task is to identify the essential concepts, and assign each a compelling purpose and a role with respect to privacy. For example, we might come up with the following concepts and purposes:

	Post: author content
	Comment: respond to content by others
	Friend: control access to your content
	Tag: identify users in images

Even these apparently simple purposes are not uncontentious. The Friend concept, for example, is also used to filter content (so that you see content that you're interested in). This purpose arguably belongs to the Follower concept, however, which in Facebook is merged into the Friend concept. But this issue need not bother us here, since privacy is our primary concern.

Now let's assign privacy roles to concepts:

	Post: determines who can edit content (the author)
	Comment: determines who can edit response (its author)
	Friend: determines who can see content (friends of the content publisher)
	Tag: no role

The privacy roles of Post and Content are straightforward: they control only who can edit the post or the comment (namely the author who wrote them in the first place). Deciding that Tag has no privacy role is obviously inconsistent with the way it behaves in Facebook, but we're laying out the simple case for now. Friend is the interesting one here, and as we'll see, what's tricky is defining who publishes given content.

As we enumerate these concepts and their roles, we realize that we have covered reading and editing (and implicitly creating, assuming that each concept governs the creation of its namesake items), but not deletion. So we add one more concept

	Owner: manage deletion of items

that will determine who is allowed to delete which items.

# Defining states

Our next task is to outline the behavior of the individual concepts. The interesting case is Friend. We start with the state components (aka the data model):

	publishes: User -> set Item
	friends: User -> set User
	sees: User -> set Item = friends.publishes

The *publishes* relation will track which items have been published by which users. Note that I wrote *Item* and not *Post* or *Comment* or *Tag*. No concept should depend on the existence of any other; we should be able to understand Friend without knowing what form the items that are published will take. From Friend's point of view, only the identity of an item matters, and *Item* can be viewed as a type variable (so that Friend is generic over all possible items).

Note also that the Friend concept maintains its own record of which item was published by which user; it can't refer to a relation in some other concept (such as which items are owned by which users in the Owner concept, or which posts are authored by which users in the Post concept). This might seem to be redundant, but in fact it isn't: we'll see that the *publishes* relation does not neatly align with these other relations in the other concepts. You can own an item but not publish it, for example.

Finally, the *sees* relation defines which items a particular user can see. It is not a state component that can be independently updated, but is instead *defined* in terms of the other state components. The expression *friend.publishes* uses the Alloy join operator, and associates with a user the set of items published by that user's friends.

# Defining actions

To complete the behavior of the individual concepts, we define actions. For Friend, for example, we might have the following actions, each with a comment giving its effect on the state:

	publish (u: User, i: Item)
	// add i to u.publishes
	
	friend (u, u’: User)
	// adds u to u’.friends and vv
	
	unfriend (u, u’: User)
	// removes u from u’.friends and vv
	
	read (u: User, i: Item)
	// blocks unless i in u.sees
	
	delete (i: Item)
	// remove item
	
Of course these specs can be written precisely in a language like [Alloy](alloytools.org) and—in addition to being precise—that would let you do automatic simulation and analysis. But that's a story for another day. The important things to note here are: (a) that the *read* action is a kind of placeholder; it doesn't really do anything except block if the user isn't allowed to see the item; and (b) that the *publish* action is not called "post" or anything that like, because it will be in the composition with other concepts that we get to decide what comprises publishing.

# Defining synchronizations

Now we can put things together, and define the overall behavior by synchronizing the actions of the individual concepts. I've only shown you the actions of the Friend concept, but I think you'll be able to figure out what the actions of the other concepts do from their names.

Here are two synchronizations:

	app fb
	includes Post, Comment, Tag, Owner, Friend, …

	// when user u adds a comment c to post p
	sync Comment.add_comment (u, p, c)
		// the author of the comment becomes its owner
		Owner.create (u, c)
		// the owners of the post become owners of the comment
		Owner.add_owners (p.(Owner/owners), c)
		// the comment is published by the publisher of the post
		Friend.publish (p.(Friend/publishes), c)

	// when user u adds tag t to item i for user m
	sync Tag.tag (u, m, t, i)
		// creator of tag becomes owner
		Owner.create (u, t)
		// mentioned user becomes owner too, so can delete
		Owner.add_owners (t, m)
		// tag itself is published by publisher of item
		Friend.publish (i.(Friend/publishes), t)

The first one describes what happens when the user adds a comment. That action produces three additional actions: the user who adds the comment becomes an owner of the comment; the owners of the post (on which the comment is made) become owners of the comment; and the comment is published by the publisher of the post. The expression *p.(Friend/publishes)* uses Alloy's nifty dot operator to navigate backwards from the post *p* through the *publishes* relation in Friend to obtain the set of users that are the publishers of *p*.

As a result of these actions, when Alice adds a comment to Bob's post, only Alice can edit the comment (since authorship is maintained in the Comment concept); both of them can delete the comment (since they are both owners, and the Owner concept lets owners delete items); and the comment is deemed to have been published not by Alice but by Bob. This last part is critical for our exploration of privacy in Facebook: it's what ensures that it's Bob's friends, and not Alice's friends, who see the comment.

The second sync says what happens when a user tags an item. Both the tagger and the tagged person are owners, so the tagged person can delete the tag if they don't like it. More interestingly, the effect of tagging includes making the tag published by the publisher of the item. So this means that the tag is visible to the same set of users as the item it's attached to.

# Modeling the way Facebook really is

This is not how Facebook works by default, although you can get this behavior by tightening your privacy settings. Here is a sync that describes Facebook's default behavior:

	// when user u adds tag t to item i for user m
	sync tag.tag (u, m, t, i)
		... // owners as before
		// tag itself is published by publisher of item
		friend.publish (i.(Friend/publishes), t)		
		// tagged person becomes a publisher of the item
		friend.publish (m, i)	
		// tagged person becomes a publisher of the tag
		friend.publish (m, t)

In this variant, the person tagged (*m*) additionally becomes a publisher of both the tag and the item tagged: that means that all their friends will see the tagging.

# What have we gained?

By describing the Facebook design in terms of concepts, we've achieved a few things:

1. We've factored the behavior into understandable pieces. In particular, the Friend concept is responsible for managing visibility, and encapsulates a very simple rule: you can see what your friends publish.

2. Because concepts can be combined by synchronization, we can describe behaviors in which actions happen not only because they are directly initiated by the user (such as adding a comment), but also because they occur implicitly, in combination with other events (such as the publishing of the comment by the author of the associated post).

3. We can now disentangle things, When Alice tags Carol in Bob's post, who is publishing what? In the design we proposed as the sane one, Bob is the publisher of the tag, just as he would be the publisher of a comment that Alice made on his post. And thus the audience of the post is not expanded by the act of tagging. In contrast, in the Facebook design, when Alice tags Carol in Bob's post, Carol is deemed to be publishing not only the tag but also the original post!

3. We can now judge the various design options, distinguishing sane and straightforward designs from those that are at best baroque and at worst malicious. For example, the case for arguing that any follow-up to a post is treated as published by the author of the post is straightforward, and corresponds to the idea of the whole discussion being "hosted" by that author. The case for the actual Facebook design, in which a tagged person is deemed to republish the post as a whole, seems much harder to defend.

# Where does this all go?

The upshot of this rather lengthy (and in places complicated) discussion can be stated simply. A user interacts with a software system by executing a series of actions. In an honest design free of dark patterns, which actions occur, and the effect of those actions, align with the user's expectations. Those expectations are set by the assumptions that the user brings to the interaction, based on prior experiences with similar systems. They include simple expectations about the user interface itself (for example, that green means go and red means stop), and deeper expectations about the underlying functionality (for example, that items don't spontaneously get added to a shopping cart). Concepts provide a way to identify recurring units of functionality, and thus to articulate these assumptions.

These ideas could be used to make apps such as Facebook more transparent and accountable to their users. An industry standards body could agree on the definition of concepts such as Friend, and Facebook would then be encouraged (or perhaps required) either to conform to that concept, or make it very clear that their concept was not compliant. Along with this, Facebook would be required to explain in simple terms who is publishing what, so that a user can figure out how actions in other concepts are interpreted as publishing actions from the point of view of Friend.

Admittedly, this isn't simple. But it's surely simpler than the status quo, in which the user is faced with a complex data model of posts, comments, replies, tags and friends, and very little structure that would allow them to understand what's really going on.

*This post has been edited, based on very helpful discussions with [Rebecca Jackson](https://www.linkedin.com/in/rebeccasjackson) and [Eunsuk Kang](https://eskang.github.io). Thank you to both of them. Thank you also to [Jimmy Koppel](https://www.jameskoppel.com) for pointing me to Joel Spolsky's [story](https://www.inc.com/magazine/20081001/how-hard-could-it-be-sins-of-commissions.html) in which he experienced "sneak into basket" in a physical store.*

