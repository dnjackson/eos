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
7. **Just**: allocating benefits amongst different groups of users and stakeholders in a fair way.

## Non-criteria

Some criteria I have intentionally omitted from this list:

- **Simple**: [Simplicity](../simplicity) is about the design itself, rather than the user’s experience of it, and it impacts all of the criteria (most notably understandability).
- **Learnable**: How easy it is to learn to use a product is part of how understandable it is. A well designed product not only conveys its states and actions for the user’s immediate understanding, but also *teaches* the user, helping build a mental model over time. The “product,” of course, must be broadly construed to include help features and training materials.
- **Consistent**: Some qualities are not ends in themselves, but means to achieving fitness, so are heuristics rather than criteria.
- **Maintainable**: Many qualities matter for developers but not for users (or at least, only indirectly); these are [engineering](../design-vs-engineering) rather than design criteria.
- **Minimalist**: Some [writers](https://www.nngroup.com/articles/ten-usability-heuristics/) advocate minimalism as a universal requirement of good design. It’s an attractive and compelling position, but it reflects a preference that is cultural and personal rather than universal. Many designers in the past (such as John Ruskin and Louis Sullivan) have argued that ornament and decoration are not surface treatments that detract from a design but are integral to it. Perhaps the pendulum will swing back again, and our affection for grey interfaces with text in Helvetica Light will wane.

## The design space
If we imagine each criterion has having a value between zero and one, then each design sits at a point in a 7-dimensional space, and we can compare designs with respect to their positions.

Comparisons are most instructive for designs that address the same problem: two email clients, for example, or two word processors.

Comparisons between designs that address different problems often reflect more on the problems than on the designs. Adobe Photoshop, for example, is less understandable than Adobe Lightroom, in that the cognitive burden of becoming familiar with Photoshop is greater. This is as expected, since Lightroom (roughly speaking) only lets you adjust the values of the pixels in a single photo, whereas Photoshop lets you combine and rearrange pixels from different photos.

## Tradeoffs
The designs that solve a given problem are not usually distributed uniformly over the design space. Instead, they sit on a plane so that increasing the value of one criterion means decreasing another.

A design that is more flexible and efficient will usually be less understandable and less robust. Take, for example, the two file managers that are available in MacOS: the Macintosh Finder (comprising the windows and icons, and manipulated mainly by clicking and dragging), and the Unix command line (accessible through the Terminal application, which takes textual commands).

The Unix design is more flexible (you can put a file in two folders, for example) and also more efficient (you can more easily move many files at once, for example). But it is at the same time less understandable. Even the most basic command, *mv*, performs two distinct actions (moving and renaming files and folders) and can behave differently when a trailing slash is added, contrary to the [assumptions](https://stackoverflow.com/questions/18158248/should-i-put-trailing-slash-after-source-and-destination-when-copy-folders) of many users. It is also less robust: if you inadvertently *mv* one file onto another (by typing *mv a b*, intending to rename *a* to *b*, unaware that *b* already exists), the second will be irretrievably lost.

Sometimes, a higher value in one criterion can mitigate a lower value in another. For example, greater understandability can make up for some loss of robustness. In the Backblaze backup utility, the message “Files backed up as of 1:00pm” gives the user the impression that file contents generated prior to 1:00pm will be safe. In fact, the backup is not truly continuous, but instead periodically creates a list of modified files and then uploads them. Files changed after the creation of the list are not uploaded. This evident lack of robustness would be mitigated by a clearer message that helped users understand the “blind spot” in the backup process.

## Notes
- The generalization of error tolerance to robustness is inspired by [Eunsuk Kang](https://eskang.github.io)’s recent work.
- Justness is a big topic, which I address in my teaching through Batya Friedman’s [Value Sensitive Design](https://vsdesign.org).
- These criteria expand on a list initially drawn up by Rob Miller for the MIT user interface design class [6.831](https://ocw.mit.edu/courses/6-831-user-interface-design-and-implementation-spring-2011/).