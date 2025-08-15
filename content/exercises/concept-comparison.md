---
author: "Daniel Jackson"
title: "Concept comparison exercise"
date: "2025-07-24"
description: "Learning about concept design by comparing similar concepts"
ShowToc: true
TocOpen: true
hideMeta: true # removes date etc from post
summary: "Concept comparison exercise"
aliases:
hidden: true 
editPost:
  URL: "https://forum.essenceofsoftware.com"
  Text: "Forum" # edit text
  appendFilePath: false # to append file path to Edit link
---

One way to improve your understanding of concept design is to compare related concepts, and try to determine (and then articulate) how and why they differ.

Confusion between related concepts can be a design flaw in itself, when the designer fails to convey (through the user interface and support materials) what concept is being offered in a particular situation.

For example, X/Twitter used to have a feature called "favorites," but it wasn't clear if this was an instance of an Upvoting concept (which collected votes from users for popularity ranking) or a Favoriting (or Bookmarking) concept (which let users save favorite tweets). In 2015, Twitter renamed "favorites" to "likes" and replaced the yellow star icon with a red heart, explaining in a press release "We are changing our star icon for favorites to a heart and we’ll be calling them likes… We know that at times the star could be confusing, especially to newcomers. You might like a lot of things, but not everything can be your favorite." Needless to say, this didn't help explain what concept was actually at play, and in 2017, Melania Trump famously "liked" a tweet that taunted her husband, presumably believing that she was bookmarking it, when in fact her "like" was shown publicly as a gesture of approval. In 2018, Twitter finally added a Bookmark concept.

In this case, it seems possible that the confusion was intentional, since public likes, unlike private bookmarks, serve the company's business goals of increasing visible user engagement (and indeed, even after they were added, the bookmarking actions were buried in the user interface and far harder to access than the upvoting actions). In other cases, confusion between concepts is simply a consequence of poor design, and even with good design it is not possible to ensure complete clarity for all users given their diversity of background and assumptions.

## Exercise: Style Concepts
Most tools that offer formatting of text or graphical objects include concepts for styling them, which allow a user to apply formatting rules that were previously defined. There is a fundamental difference between a concept that merely stores formatting rules as "styles" and one that maintains associations between objects and their styles.

To explore this difference, start by experimenting with some of the following concepts, using whichever applications you have convenient access to or are familiar with, selecting at least one example from each category:

Category A
- Paragraph styles in Microsoft Word, Apple Pages and Google Docs
- Color swatches in Adobe Indesign
- Site styles in Squarespace
- Selection colors in Figma
- Color themes in Visual Studio Code
- Shape styles in Apple Keynote
- Theme colors in Microsoft Powerpoint

Category B
- Color palettes in the Apple color picker
- Text color selector in Google Docs
- Cell styles in Microsoft Excel
- Layer styles in Adobe Photoshop
- Shape styles in Microsoft Powerpoint
- Text styles in Apple TextEdit

Now articulate the difference between these categories by formulating two concepts, one for each category. Make your concepts simple and generic; they do not need to capture all the details of a particular example. Point to the essential difference in behavior between the two concepts (in operational principle and actions). Do the two concepts share a purpose, or are their purposes different? Is one easier to implement than the other (hint: look at the concept state)?

## Exercise: Passwords and Personal Access Tokens
Github offers a form of authentication called [personal access tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens). This is described as "an alternative to using passwords." [Creating a token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-personal-access-token-classic) sounds very much like creating a password with a strong password helper: you click a button to create a token, and get an obscure string which you can then save. [Using a token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#using-a-personal-access-token-on-the-command-line) appears to be the same as using a password: you enter a username and the token string, and will then be authenticated if the string matches the one that was generated when the token was created. Moreover, at the very start of the [article](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-personal-access-token-classic) we are told "Treat your access tokens like passwords."

So what exactly is the difference between the standard Password concept and the PersonalAccessToken concept? Read the Github page carefully\*, and document the two concepts, paying particular attention to their purposes and operational principles. Now consider how they differ, and explain how their behavior is motivated by different purposes. You will also want to consider whether one of the concepts has actions that are not provided by the other concept.

\* Note: consider only "personal access tokens (classic)" and not "fine-grained personal access tokens."