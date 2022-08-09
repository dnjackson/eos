---
author: "Daniel Jackson"
title: "Generic fitness criteria"
date: "2022-08-02"
description: ""
ShowToc: false
TocOpen: true
hideMeta: false # removes date etc from post
summary: ""
editPost:
    URL: "https://forum.softwareconcepts.io"
    Text: "Comments" # edit text
    appendFilePath: false # to append file path to Edit link
---


## Seven criteria

What makes a design fit for purpose depends—surprise!—on the purpose. But generic criteria are helpful for two reasons. First, they give you some basis for evaluation, however crude. Second, they can be used as a checklist to suggest more specific ones. 

A software product should be:

1. **Efficient**: letting users accomplish their goals with no more steps or effort than needed;
2. **Flexible**: supporting not just one fixed and narrow goal, but a range of goals that users are likely to have;
3. **Responsive**: responding to requests from the user in a timely way;
4. **Understandable**: behaving predictably, offering options and producing outcomes that make sense to users;
5. **Robust**: tolerant of errors made by the user and other common perturbations of context, such as failures of other systems or agents to perform their expected roles;
6. **Secure**: resistant to malicious attacks, including social engineering attacks that induce the user to perform unintended actions.
7. **Beneficent**: bringing good to its users and to society as a whole.

## Non-criteria

Some criteria I have intentionally omitted from this list:

- **Maintainable**: Many qualities matter for developers but not for users (or at least, only indirectly); these are [engineering](../design-vs-engineering) rather than design criteria.
- **Consistent**: Some qualities are not ends in themselves, but means to achieving fitness, so are heuristics rather than criteria.
- **Simple**: Simplicity is also a means rather than an end, albeit an [essential one](../simplicity). It’s a dangerous criterion for comparison between products, because more complicated purposes often demand more complicated solutions. Photoshop cannot be as simple as Instagram (although perhaps it can be simpler than it is).
- **Minimalist**: Some [writers](https://www.nngroup.com/articles/ten-usability-heuristics/) advocate minimalism as a universal requirement of good design. It’s an attractive and compelling claim, but it seems to me a preference that is cultural and personal rather than universal. Although a view that is out of fashion nowadays, many designers (such as John Ruskin and Louis Sullivan) have argued that ornament and decoration are not surface treatments that detract from a design, but are integral to it.
- **Learnable**: I’ve excluded criteria that are not obviously universal. All products must be understandable, but how quickly a user can be expected to come to an understanding must depend on the product. So I prefer to see understandability as context-dependent. Adobe could reasonably claim that Photoshop is understandable once you’ve spent a few hours studying it, but the same claim by the New York subway about its ticket machines would not fly.

## Examples
trash?


## Notes
- The generalization of error tolerance to robustness is inspired by Eunsuk Kang’s recent work.
- Initial list from Rob Miller & co, 6813.
- beneficent is a big topic: See VSD.


Qualities cannot be specified, but can be anticipated, evaluated and tested; usually in tradeoff with each other. Examples: Trash (error tolerant, easy to learn, but not very flexible).