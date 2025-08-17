---
author: "Daniel Jackson"
title: "System design writing exercise"
date: "2025-07-24"
description: "Learning about system design by writing concept specs and syncs"
ShowToc: true
TocOpen: true
hideMeta: true # removes date etc from post
summary: "System design writing exercise"
aliases:
hidden: true 
editPost:
  URL: "https://forum.essenceofsoftware.com"
  Text: "Forum" # edit text
  appendFilePath: false # to append file path to Edit link
---
In this exercise, you'll design a few concepts and some synchronizations to fulfill the features of a simple app. Then you'll modify your concepts (if necessary) to make them sufficiently modular to be used for some variants of the app. You should include the synchronizations that are needed for the primary web requests that define the app, and should give some examples of how they might be modified to account for the variants in functionality.

## Application Description
The app is a simplified audience polling tool like [Slido](http://slido.com), which allows presenters to create simple questions that are then presented to an audience. Members of the audience answer the questions and the presenter can see (and share) aggregate results. A question may have a simple short textual answer, or it may ask for selections from some predefined options; it's up to do you to choose, but don't make it complicated by offering both.

## Application Variants

Here are the application variants that your design should support without changing the essential concepts:
- Displaying the polling link:
	- Variant A: the link is displayed as a QR code
	- Variant B: the link is displayed as a short URL
- User accounts:
	- Variant A: creators of polls must have accounts and can view all the polls they created in one view
	- Variant B: creators of polls do not have accounts, but received a link whenever they create a poll through which it can be viewed and edited later
- Expiry:
	- Variant A: polls do not expire.
	- Variant B: polls expire some fixed period of time after they have been created.
	