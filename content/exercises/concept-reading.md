---
author: "Daniel Jackson"
title: "Concept reading exercise"
date: "2025-07-24"
description: "Learning about concept design by reading a concept spec"
ShowToc: true
TocOpen: true
hideMeta: true # removes date etc from post
summary: "Concept reading exercise"
aliases:
hidden: true 
editPost:
  URL: "https://forum.essenceofsoftware.com"
  Text: "Forum" # edit text
  appendFilePath: false # to append file path to Edit link
---

To help you get going with designing individual concepts, this exercise asks you to read a mostly complete concept and answer some questions about it.

**concept** GiftRegistry \[User, Item\]

**purpose** track purchases of requested gifts

**principle**
a recipient creates a registry, and adds items to it indicating the number of each requested; opens the registry so it becomes publicly visible; then givers can view which items are still available and purchase them; and finally the recipient closes the registry, after which it is no longer publicly visible but the recipient can see which items were purchased and by whom.

**state**
a set of Registrys with
	an owner User
	an active Flag
	a set of Requests
a set of Requests with
	an Item
	a count Number
	a set of Purchases
a set of Purchases with
	a purchaser User
	an Item
	a count Number

**actions**
create (owner: User): (registry: Registry)
	create a new registry with this owner, active set to false and no requests
addItem (registry: Registry, item: Item, count: Number)
	requires registry exists
	effects if a request for this item exists, add the count
	  otherwise create a new request for the item with this count and add to registry
removeItem (registry: Registry, item: Item)
	requires a request for this item exists in the registry
	effects remove the request from the registry
open (registry: Registry)
	requires registry exists and it is not active
	effects make registry active
close (registry: Registry)
	requires registry exists and it is active
	effects make registry not active
purchase (purchaser: User, registry: Registry, item: Item, count: Number)
	requires registry exists, is active and has a request for this item with at least count
	effects create a new purchase for this purchaser, item and count and decrement the count in the matching request

## Questions
- **Invariants**. What are two invariants (aka integrity constraints) of the state? What are their purposes? Is one more important than the other? Which actions are primarily responsible for preserving each of the invariants, and how do they do it?
- **Fixing an action**. Can you identify an action that potentially breaks one of these invariants, and explain how this might happen? Can you suggest two different ways in which it might be fixed, and give some reasons in favor and against each (pointing to how they might affect users)?
- **Inferring behavior**. The operational principle describes the typical scenario in which the registry is opened and eventually closed. But a concept specification often allows other scenarios. By reading the specs of the concept actions, determine whether a registry can be opened and closed repeatedly. What might be some reasons to allow this?
- **Registry deletion**. There is no action to delete a registry. Why in practice is this probably a good design decision?
- **Queries**. What are some common queries that you would expect to be executed against the concept state?
- **Hiding purchases**. A common feature of gift registries is to allow the recipient to choose not to see purchases so that an element of surprise is retained. How would you augment the concept specification to support this?
- **Generic types**. The User and Item types are specified as generic parameters. The Item type might be populated by SKU codes, for example. Explain why this is preferable to representing items with their names, descriptions, prices, etc.
- **Assigning functionality**. For each of these possible extensions, say whether supporting it should involve changes to this concept, synchronization with other concepts (which you should name) or both:
	- Showing most recently purchased items
	- Including gift messages from givers
	- Grouping requests by category (kitchen, bath, etc)
	- Notifying recipients when a purchase is made
	- Incorporating requests for money rather than items
	- Allowing givers to join together in purchasing an item
