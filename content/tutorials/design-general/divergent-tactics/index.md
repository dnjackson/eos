---
author: "Daniel Jackson"
title: "Tactics for divergent design"
weight: 190
date: "2023-09-11"
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
# Tactics for divergence
Let’s consider some tactics that you might find useful as a software designer to make divergent design more effective. To make these concrete, I’ll suppose that we’re developing an app to support architectural walking tours.

## Interviewing stakeholders

Talking to potential users and other stakeholders is good not only for identifying needs but also for getting design ideas. You can ask what tools and techniques a user currently finds helpful, and what changes might make them more effective. Just resist the temptation to ask whether a particular design idea would be attractive; most interviews (even if just out of consideration for the interviewer) are likely to express unwarranted enthusiasm.
- *Example*: I asked a potential user who often walks around cities with her family what snags she experienced. She told me that family members would often get separated, and that it could be hard to figure out where everyone was and get back together again. This suggested several potential features to me (for example, showing avatars for all members of the group on a shared map) .

## Imagining a situation

Imagine yourself in a situation that calls for the app that you are designing. Think systematically through what you might be doing, and simulate your application ideas in your mind’s eye.
- *Example*: When I’m taking a walking tour, I’m usually on vacation, and (especially if it’s a long one) I like to have coffee breaks along the way. So I’d like to be able to plan a route around cafes with great coffee.

## Gap analysis

Consider existing apps and the gaps between the features they offer and needs you have identified. 
- *Example*: I’ve looked at several apps for architectural tours, and (surprisingly) none of them integrate a live map, so it’s hard to match the descriptions of buildings to your location.

## Viability

For an app to be successful, fulfilling some users needs may not be enough. Without a financial model, the app may not be sustainable. And many apps require a critical mass of users, or at least benefit from a network effect (in which the value of the app to open user increases with the number of overall users). So think about how an app may be monetized and costs covered (or how costs may be reduced) and how the user base might be expanded.
- *Example*: How will architectural descriptions be obtained? Perhaps individual owners (whether homeowners or institutions) could provide data about their own buildings. Users could be offered subscriptions to cover content costs. To avoid such costs, information about many buildings could be provided by Wikipedia. Discounts for tourers at local cafes, bookshops and other businesses relevant to walking tours might be offered through the app. To incentivize users to introduce others to the app, users might be able to share favorite buildings or entire routes with friends; they might even be able to generate postcards that combines interesting facts with photos.

## Analogies to other apps

Think about apps that serve similar purposes, or that address some of the same subproblems.
- *Example*: When considering subscription features, I started wondering about the models offered by blogging platforms such as Medium and Substack, and explicit support models as in Patreon. Individual content providers might be rewarded by a profit share.

## Feature lists

Scan some lists of features of existing apps. Some features might fit your app directly; others might be useful if adapted; and just considering a large number of features might spur new ideas. Places to look for feature lists include: company webpages (such as a page from Apple listing features of [MacOS Ventura](https://www.apple.com/macos/ventura/features/)), Wikipedia articles (such as this [list of Facebook features](https://en.wikipedia.org/wiki/List_of_Facebook_features)), and blog posts in which users talk about features they wish apps had (such as [this list for browsers](https://www.hongkiat.com/blog/browser-features-most-wanted/)).
- *Example*: I looked at a list of [essential travel app features](https://www.miquido.com/blog/tourism-leisure-mobile-apps/). The first listed feature was booking for flights and other things, and it occurred to me that it would be good to know if any buildings on the walking tour allowed visits, but only at certain hours or with booking in advance.

## Using an LLM
Large language models such as GPT  are very effective at generating features ideas for an app. The only thing to be wary of is that they can be so fast and seemingly comprehensive that it’s easy  to be intimidated and not go to brainstorm your own, more creative, features.
- *Example*: I gave GPT 3.5 the following prompt: “I’m designing an app to support architectural walking tours. It would help people walking around a city identify buildings and learn about them. Can you help me by suggesting some features that such an app might have?”. It generated a list of about 20 features, including ones I’d already thought of (such as augmented reality integration) and ones I hadn’t (such as showing original blueprints and building plans).

# Exploring Values during Divergent Design

Software designers are increasingly coming to see the social and ethical impacts of their products as central. In short, they want to bring their *values* to their design work. But this can’t just be tacked on as yet another design phase. Values can’t be incorporated as an afterthought but need to be integrated into your design work.

The divergent design phase provides a good context for considering social and ethical questions. By thinking about the value implications of your design ideas, you can make sure that your design is going in a direction that will be net positive to society.

This has several benefits. Most obviously, not building something that causes damage to society is a moral imperative. And building something that contributes positively to society can bring meaning and satisfaction to your work. 

There are benefits for your organization too. Companies that are regarded as more trustworthy and honest by consumers reap a big advantage in market share and popularity. 

Perhaps most relevant to our discussion are the benefits that these considerations bring to the product itself. Social and ethical considerations sometimes suggest eliminating some features. But just as often they can lead to new features, and improvements in the app overall.

Here are some examples of social and ethical considerations, drawn from the envisioning cards of value-sensitive design, applied to our example app.

## Indirect stakeholders

Indirect stakeholders are people who may be affected by a system without directly using it. A good design must them into account.
- *Example*: What if some buildings became popular in the app for reasons that might upset local inhabitants? An apartment block in London, for example, experienced a famous fire in which dozens of occupants were killed. This suggests that features that use crowdsourcing to focus attention on certain buildings and routes should be deployed carefully, and some kind of flagging of moderation feature might be needed.

## Differing abilities

Different users have different physical abilities, and a software design should accommodate these whenever possible.
- *Example*: Blind users will not be able to see photos of buildings. This suggests that photos should have captions for a screen reader, and that a separate audio feature would be useful. Like many accommodations for disabled people, including audio would benefit all users, many of whom might prefer to listen to a commentary as they walk without having to check their phones.

## Considering children

An app designer should remember that even apps that are not designed with children in mind can end up being used by children.
- *Example*: Children often accompany their parents on walking tours, so thinking about how our app might serve them is important. One possibility is that descriptions are tagged by age and expertise level, and a user can set that level at the start and only be shown appropriate content. Or we might introduce some gamification features, such as rewarding children with points for spotting particular architectural features. Again, these features can benefit all users: there are adults who like game features too.

## Diverse geographies

A solution designed for one geographic setting may perform quite differently in another.
*Example*: River tours are popular in many cities (obviously in Venice and Amsterdam, but in many others, such as London and Chicago too). In designing our app, we might want to make sure that it’s still possible to use it if you’re not walking.
