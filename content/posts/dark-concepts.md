---
author: "Daniel Jackson"
title: "Dark Concepts in Software Design"
date: "2023-03-27"
description: "The building blocks that social media apps use to control us."
ShowToc: false
TocOpen: false
hideMeta: false # removes date etc from post
# hideSummary: true
summary: "The building blocks that social media apps use to control us."
editPost:
    URL: "https://forum.essenceofsoftware.com"
    Text: "Comments" # edit text
    appendFilePath: false # to append file path to Edit link
---

## The risks of computing

From the very birth of computing, enthusiasm for its benefits has been accompanied by concerns about its risks. These concerns can be placed into three broad categories, corresponding to three periods in which they came to the fore: from the advent of computing to the late 1980s; from the 1990s through to around 2010; and from then until today.

Early on, the primary concern was the possibility of catastrophes resulting from unreliable systems operating in critical domains such as aviation, defense, space exploration, medicine, etc. Because of investments in safety (and perhaps also because of sheer good luck), in most of these domains, catastrophes caused by computers have been extremely rare. One major exception is in medicine, where devices routinely fail with fatal consequences, and many of the companies that make them seem not to have adopted the methods and techniques that have worked well in other domains.

In the 1990s, when most systems (including many critical ones) became accessible through the public internet, malicious attacks became widespread. This changed the calculus: whereas previously the potential cost of a catastrophe might be mitigated by the low probability of its occurrence, now, with sufficient financial incentive (for individual actors) or political value (for state actors) the likelihood of a vulnerability being exploited became a near certainty. Miraculously, despite the widespread of occurrence of attacks over the last 30 years, almost all consequences have been financial and none have resulted in major loss of life.

Since about 2010, a different kind of concern has emerged from a confluence of three developments: pervasive use of smart phones, growth in social media platforms, and the advent of AI algorithms. These all happened at roughly the same time. The iPhone came out in 2007, and had [100m owners](https://www.demandsage.com/iphone-user-statistics/) by 2011. YouTube was the first platform to exceed 100m users in 2007; Facebook had 100m in 2008 and [passed a billion](https://ourworldindata.org/rise-of-social-media)) by 2013. Facebook introduced likes in 2009, and began to develop [algorithms](https://en.wikipedia.org/wiki/EdgeRank) to determine the content of a users’ feeds; reportedly, they adopted a machine learning approach in 2011, one year before the demo of AlexNet, the image classifying network that inaugurated the era of deep learning.

The concern now is damage to our social fabric: that AI algorithms used to make decisions in hiring, financial services, policing, sentencing, and so on, perpetuate or even promote bias; that chat platforms cause pressure, especially in teens, leading to higher rates of depression, anxiety and suicide; and that the engagement-chasing tactics of social media encourage the spread of disinformation and exacerabte political fragmentation and polarization.

Another theme, less dramatic but perhaps as significant economically, is the propensity of software technology to sap attention from users, distracting them from productive work and burdening them with mundane and time-consuming data processing tasks. This wastage might explain in part the “digital stagnation” in which increases in productivity in the US slowed since the start of the 1980s (when the personal computer was introduced).

## How to save the day?
How can we prevent further damage to our social fabric, without abandoning the advantages of software?

Some people believe that adding more technology can counteract the problems of existing technology: the [decentralized web](https://docs.google.com/presentation/d/1SaHGyY9TjPg4a0VNLCsfchoVG1yU3ffTDsPRcU99H1E/mobilepresent?slide=id.g11b904107df_0_1), blockchain, multiparty computation, end-to-end encryption, differential privacy, and so on. Interestingly, despite the excitement about the potential of machine learning, almost nobody thinks that it’s a technology that will make the social problems of software easier to solve; in fact, it will probably make things worse. Some of these technologies are indeed promising but none is likely to be a panacea. And most of them provide means to limit communication and increase privacy, which does not always align with how users want to use software.

Others believe that regulation can force companies to eliminate the social risks of software. Terminating some bad practices (eg, the selling of users’ personal data without consent) would undoubtedly help, but it’s become clear that legislating away the problems of social media is a pipe dream. And the sad example of GDPR-mandated cookie controls shows how design-by-government can backfire.

The problems caused by software are not surprises, but are direct consequences of its design. This suggests that the most direct path to fixing software is via design, first by identifying problematic design elements and then devising remediations to fix them. Technology and regulation may play supporting roles, but changing software designs must be central.

## Problematic design elements: dark concepts

Almost all of the negative impacts of software on our lives—whether in stealing valuable attention, or tearing apart the social fabric—can be attributed to a small number of design elements. 

These elements can be described as *[concepts](https://essenceofsoftware.com/posts/distillation/)*, units of functionality that are independent of one another (and hence can be understood one at a time), and reusable (and hence can do damage in many different settings).

I call concepts that cause trouble *dark concepts*, using the terminology of dark patterns. But whereas dark patterns are often quite egregious attempts to deceive, dark concepts are usually more subtle. They lie deeper within an application, and are harder to identify and root out, in part because dark concepts often also fulfill legitimate purposes.

## Upvote: a dark concept

An example of a problematic concept—and one that has been much discussed—is what I call *[Upvote](https://essenceofsoftware.com/posts/upvote/)*. The apparent purpose of this concept is to use the wisdom of the crowd to evaluate the quality of items. Like most concepts, *Upvote* is polymorphic, and can be applied to any kind of item: photos in Instagram, comments on New York Times articles, tweets in Twitter. The concept is perhaps most effective in Q&A apps such as StackExchange and Quora, where the number of upvotes often corresponds reliably to the credibility of an answer.

The Facebook incarnation of *Upvote*, in particular, has been the subject of much hand-wringing. Upvotes, originally called “likes” in Facebook (on launch in 2009), were problematic from the start, since users had a tendency to “like” photos of cats more than posts containing thoughtful life reflections.

In 2016, Facebook introduced “reactions.” This was actually a synchronization of two distinct concepts: a *Reaction* concept which, like that concept in apps such as Slack, allowed a reader to select an emoji in response to a post, and the *Upvote* concept, which used the number of likes to promote popular posts. 

Controversially, the design did not allow *Upvote* and *Reaction* to be controlled independently, but instead automatically turned reactions into upvotes. Worse, it seems that emotional reactions were converted into upvotes with higher weightings than simple likes. Leaked reports from Facebook claimed in particular that reacting with the angry emoji was equivalent to five regular likes in terms of the impact on a post’s promotion. 

The *Upvote* concept therefore plays a central role in the dissemination of negative and sensationalist content. Like other dark concepts, it has a plausibly beneficial purpose (using crowdsourcing to promote higher quality content) which might have motivated its original design, and which may still be primary in some applications. But in Facebook, and many other apps, that purpose has been supplanted by a far less innocuous one—increasing engagement at all costs—so that a seemingly straightforward concept has turned dark.

## Other dark concepts

As apps, and social networking apps in particular, have evolved, a cluster of concepts has emerged, shaping users’ experiences in often negative ways. 

### The Feed Concept

Every concept has an “operational principle”: an archetypal behavior that users have in mind. For example, the principle for the concept of *EmailMessage* is (roughly) that if you send a message to someone whose email address is listed in the to-field, then sometime later a copy of that message will appear in their inbox.

A similar kind of principle once applied to the *Feed* (or *Timeline*) concept in social media apps such as Facebook and Twitter: that users would create posts, and their connections would subsequently see them in their feeds.

Of course, it was never quite so simple: the *Upvote* concept determined the ranking of posts, and the *Friend* and *Follower* concepts determined which posts were included. These other concepts were perhaps sufficient to explain the behavior, and it was not clear that a separate *Feed* concept was needed.

But over time, the implicit operational principles of these concepts faded, and social media companies exploited their unpredictable behavior to their own advantage. The *Upvote* concept no longer determines ranking of posts in any simple way; nor does *Friend* limit the posts you see to your friends. Instead, these concepts provide input to the *Feed* concept which, by way of inscrutable and undocumented algorithms, shows you the posts the company wants you to see.

These posts include, of course, “sponsored posts” (aka advertisements), and they are selected not only on the basis of your explicit upvotes but also on implicit aspects of your behavior, the wording of your posts, your profile and demographics, and probably also various forms of personal information obtained without your consent from third-party data brokers.

On most platforms, there is no way to override this unpredictable and uncontrollable behavior, for example by asking to see posts from friends ordered by upvotes and/or time of posting.

Twitter reluctantly allowed users to switch to a “chronological timeline” but the company-curated timeline is the default. In March 2022, Twitter [decided](https://9to5mac.com/2022/03/14/twitter-no-longer-lets-users-access-the-chronological-timeline-by-default/) to no longer allow users to switch their default, but then reversed after complaints.

### The Playlist Concept

In Spotify, a dark concept similar to *Feed* has become prominent. The seemingly innocent *Playlist* concept has apparently become so important to Spotify’s business model that [Corey Doctorow](https://doctorow.medium.com/freedom-of-reach-is-freedom-of-speech-6fc203d136e0) coined the term “playlistification” to describe the pernicious impact of this concept on the app as a whole.

Go to your home page in Spotify, and you’ll find that it’s completely dominated by playlists: playlists you recently played, playlists of artists you might like, 
playlists curated specially for you, and so on. You’ll see that many of the recommended playlists are marked “by Spotify.” The rest are marked as “public playlist,” suggesting that they might be listener-curated lists that were made public. Look carefully, however, and you’ll see that almost all of these name Spotify as the curator.

In practice, this means that, through the *Playlist* concept, Spotify not only decides what playlists will be recommended to you, but even what the content of those playlists will be. Doctorow cites this as a prime example of “chokepoint capitalism” and alleges that Spotify extracts payment from record labels in return for placing their artists’ tracks on popular playlists.

This kind of strategy might be called “reintermediation.” The web brought disintermediation, in which companies eliminated go-betweens, allowing consumers to purchase services directly from suppliers. This model is at the core of the gig economy (eg, in Uber and Lyft) and the marketplaces created by online retailers (such as Amazon and Etsy). 

But then the companies that provided the disintermediation realized there was money to be made by reinserting themselves, and controlling the relationship between producer and consumer. This reintermediation is seen not only in concepts such as Spotify’s playlist but also in the concept of *Recommendation* in which a company recommends products or services to a user supposedly on the basis of that user’s prior activity, but also based on the company’s preferences of which suppliers and products to favor. 

The *Rating* and *Review* concepts, in which concepts provide numerical ratings and textual reviews of products and services, are ripe for abuse. Ratings and reviews have enormous impact on consumer choices, and must inevitably be filtered to prevent fraudulent use, so the simplest and most transparent algorithms are not feasible.

[Yelp](https://en.wikipedia.org/wiki/Yelp), in particular, has been the subject of many [allegations](https://www.forbes.com/sites/jimhandy/2012/08/16/think-yelp-is-unbiased-think-again/?sh=15c178b611d1), including that advertising salespeople offered to suppress negative reviews in return for a company purchasing advertising. Yelp’s business practices were the subject of a documentary called *[Billion Dollar Bully](https://en.wikipedia.org/wiki/Billion_Dollar_Bully)*. None of these accusations has been substantiated in court, however. 

### The Tag Concept

*Tag* is another concept whose purpose seemed to be innocuous at first. Allowing users to identify friends in photos offered a simple way to bring photos to their subject’s attention, and to make it easier to search for images in which particular friends appeared.

It became clear, however, that the real purpose of the concept is more insidious. In fact, there seem to be two distinct dark purposes. One is to expand the density of the social graph, by sharing a user’s posts beyond their friend group: in Facebook, by default, if a user is tagged in a post, that post is shared with the user’s friends, including those who are not friends of the original poster. The other is to harvest more personal data from users by tracking who appears together in photos, and in whose photos. 

Not surprisingly, Facebook was unsatisfied with relying on manual tagging and was eager to streamline the process, so it began to use facial recognition algorithms to detect faces automatically and make tagging suggestions to users. A [class action suit](https://www.cnet.com/tech/services-and-software/facebook-privacy-lawsuit-over-facial-recognition-leads-to-650m-settlement/) was filed in Illinois in 2015 alleging a violation of the state’s Biometric Information Privacy Act; in 2021, the suit was settled, with Facebook paying $650m to affected users.

### The Cookie Concept

Dark uses of the *Cookie* concept have been a concern for more than a decade. As in all these examples, the original purpose was reasonable. In this case, it was to compensate for the “stateless” nature of HTTP, the underlying protocol of the web.

Because HTTP, in contrast to other protocols (such as FTP) treats each request independently, rather than as part of a session, it offers no built-in way to remember a user’s prior interactions with a server. Any context must therefore be maintained by a separate mechanism. This is where cookies come in. When you first access a website, it sends a cookie back that is installed in your browser; this cookie is then forwarded back to the website’s server with every subsequent request. The interaction thus becomes “stateful,” with the state held in your browser (in the form of the latest version of the cookie), and in the server (typically in a database table indexed on cookie identifiers).

Such a mechanism is indispensable. It’s what allows sessions (allowing you to log in just once and have requests that follow be treated as authenticated); it supports preferences that govern how a website behaves; it allows more context-specific behavior; and it makes it possible to add items to a shopping cart before you’ve logged in to an online store.

The cookie concept supported another more controversial purpose: letting websites track users. By depositing a persistent, unique identifier in your browser, a website can track you across sessions, whether or not you are logged in (or even have an account). I noticed a slick use of this strategy just a few weeks ago: I visited a page on a tea store website (teabox.com) and within minutes received an email coincidentally informing me that the tea I had looked at was on sale. I was not logged in at the time, but since I had an account, the server was able to correlate my cookie with my email address.

The privacy implications of cookies were exacerbated by advertisers. Although a cookie can only be sent back to the domain that served it, a single webpage can contain fragments (most notably advertisements) loaded from a different domain, with their own cookies. By around 2014, it was not unusual for webpages to carry hundreds of these so called “third party cookies.” 

In 2020, Apple started blocking third-party cookies in its Safari browser, and Google is promising to follow suit in Chrome by the end of 2024. But first-party cookies are still ripe for abuse. The European Union’s GDPR forces companies to obtain explicit consent for cookie usages, but the consent mechanism is ignored by most users and has just become another annoyance.

### The Notification Concept

The *Notification* concept has many legitimate uses. For example, if you post a question on a forum that you rarely visit, you will likely want to be notified when a response appears. Likewise, most users appreciate notifications that items they’ve ordered have been shipped, or that a returned item has been accepted.

Increasingly, however, companies use notifications to draw users to an app with much greater frequency than they would willingly choose. LinkedIn, for example, sends notifications whenever a user receives a message. Insidiously, such messages are often advertisements that are posted as if they were communications from contacts. And by omitting details about the message—its sender, subject or any part of its contents—from the notification (which appears, for example, as an email), the user is forced to visit the app to discover that the interruption is unwarranted.

### Other Problematic Concepts
Other concepts might be implicated in a variety of social and psychological harms, but have been less consciously manipulated by companies for their own benefit.

For example, the *Presence* concept included in apps such as Slack, which allow users to indicate whether they are available, is ripe for abuse in work environments in which managers track their employees and encourage them to be constantly online and interruptible. More troubling versions of this concept have been deployed. A friend reported that Skype for Business used to determine presence automatically from mouse movements, and that she and her colleagues felt obligated to move the mouse every couple of minutes to ensure that they would be seen to be working.

The *Reaction* concept, in which users can respond to social media posts with emojis signaling approval or disapproval, seem to be a mere convenience, and provide some benefit in reducing the burden of lengthier responses. But for teenage girls, in particular, the pursuit of online approval has devastating impacts on mental health.

## Responding to Skepticism

Complaints about the negative impact of apps and social media reasonably evoke some skepticism. A few questions especially come to mind.

First, one might ask: **is the damage inflicted by these dark concepts enough to matter**? In response, I would note first that the impact of dark concepts should be assessed in aggregate across all users. Even if most individual users do not suffer extensive harm, the overall damage (to the social fabric, and in some cases to total productivity) may be significant. Moreover, some users will suffer more than others; the dramatic growth of depression and anxiety in young people has been attributed by some to use of phones and social media.

Second, one might wonder: **do users really care?** Perhaps most users are satisfied with their apps and they feel that they get more than enough benefit to outweigh the costs. As habits and cultural assumptions shift slowly over time, however, users might not be aware of the extent to which their judgments are conditioned by their environment. In some cases (for example, loss of privacy) the damage may not be seen at the time, and only becomes evident later (for example, when personal data is used in a bad way). Users may also be unaware of the costs they are suffering: they might be less pleased about Spotify’s recommendations if they come to realize that those recommendations were made to all of their peers and were paid for by record labels. And of course sometimes the users are not the ones who suffer. In the case of Spotify’s playlists, Doctorow argues that the prime victims are artists and record labels for whom payments to promote their material are a kind of legal bribe.

Some commentators (such as [Brian Caplan](https://www.econlib.org/a-world-of-ingratitude/)) argue that the very fact that consumers continue to use platforms and apps indicates their “revealed preferences” and shows that, whatever they might say, they are happy with the tradeoff they have made (often accepting dark behaviors in return for not having to pay). This argument would be stronger if good alternatives were available so we could actually observe users’ preferences for one option over another.

Moreover, this argument militates against any concerns about product quality so long as companies are transparent. It would suggest, for example, that companies should be able to offer food products that are contaminated so long as purchasers realize that they are receiving a discounted price in return for a higher chance of being poisoned. But of course the key point with social media companies is that they are *not* transparent, and they go to considerable lengths to present dark concepts as being beneficial to the consumer alone.

If some of these questions could be formulated more precisely, it might be possible to design experiments or data analyses to answer them more rigorously. Companies that make software might be interested to know if dark concepts actually pay off. They might be very successful in the short term in meeting goals for engagement or advertising, while nevertheless annoying consumers enough to be more negative than positive in the long term. It would be interesting to know whether the metrics-driven culture of many companies (in which each unit is evaluated in terms of easily measurable but not necessarily reliable metrics of success) is a factor in the development and prominence of dark concepts.

## Closing Thoughts

**Dark patterns**. Harry Brignull, a British UX designer, coined the term [dark pattern](https://www.deceptive.design) back in 2010, and started cataloging examples of websites that used a variety of common [patterns](https://www.deceptive.design/types) to deceive users, from “Roach Motel” (the subscriptions that were easy to start but near impossible to end) to “Privacy Zuckering” (sharing more personal information than intended).

Now, more than a decade later, although these kinds of dark pattern are still [common](https://www.deceptive.design/hall-of-shame/all), they seem almost quaint in comparison to the insidious schemes that lie more deeply beneath the user interface. These schemes, which I’ve described in this post as a handful of dark concepts, go beyond getting us to perform actions that we might not have intended. They take control away from us, and shape our experiences in ways that are often opaque and contrary to our true interests.

**Data science rubrics**. Many of the dark concepts are enabled by the data science technologies that have emerged over the last decade as companies have collected and exploited vast banks of data about their customers. A recent new book ([Data Science in Context](https://datascienceincontext.com)) presents rubrics to help designers identify and address the kinds of concerns that I have raised here. A section of the book on personalization, for example, outlines the risks posed by algorithms for targeting advertising and making recommendations. This offers a dual perspective to mine, focusing on the developers’ *objectives* and the data science methods used to achieve them, where I have instead pointed to *concepts*—units of functionality which, while broadly motivated by those objectives, form the building blocks of modern applications.

**Fixing social media**. How can a concept design lens help fix social media? First, viewing social media apps through their concepts helps make clear not only how pervasive the problems are, but how similar they are across apps. Second, they dispel the misapprehension that the problems are complicated and inscrutable. While the algorithms that lie behind dark concepts may be subtle, the concepts themselves, whose design makes room for those algorithms—the Feed concept for example priming the user to abandon all expectations of how the posts displayed will be selected—are easily grasped by users, and indeed, experienced daily. Third, concepts give a point of leverage for improvement; each concept can be addressed in isolation and fixed without impacting other concepts. Fourth and finally, concepts offer a way to codify best practices—for example, a *Tag* concept that enriches search while respecting the privacy of users—thus supporting industry-wide efforts to reform application design (and to indemnify developers eager to do the right thing but not sure how to do so).

**AI-augmented applications**. As machine learning becomes an important part of all applications, the kinds of issues I’ve discussed here will become even more important. As a [recent op-ed](https://www.nytimes.com/2023/03/24/opinion/yuval-harari-ai-chatgpt.html) put it:

*“Social media was the first contact between A.I. and humanity, and humanity lost… Large language models are our second contact with A.I. We cannot afford to lose again.”*

Perhaps the concept framework can help not only articulate the roles that AI algorithms play in apps, but also contain them, placing them in sandboxes that allow users (and society more broadly) to understand, if not exactly how they work, then at least the boundaries of their functionality, which behavioral expectations can be relied upon, and which plausible assumptions that users adopted in their naivety will have to be relinquished.