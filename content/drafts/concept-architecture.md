---
author: "Daniel Jackson"
title: "Concept Architecture"
date: "2024-08-12"
description: "A Proposal for a Concept-based App Architecture"
ShowToc: true
TocOpen: true
hideMeta: false # removes date etc from post
summary: "A Proposal for a Concept-based App Architecture"
editPost:
  URL: "https://forum.essenceofsoftware.com"
  Text: "Comments" # edit text
  appendFilePath: false # to append file path to Edit link
---

# Background
Our Kodless prototype seems to be very promising and allows effective LLM-based code generation of both concepts and syncs, as demonstrated by the HackerNews example.

Some limitations and opportunities:
- **Catalog**. Currently all concepts are generated afresh, aside from some basic ones like Session. It would be easy to extend Kodless to allow concepts to be selected from a catalog. This would allow (a) reuse; (b) greater confidence in the security of critical concepts; (c) concepts that might be hard to build with an LLM because they involve tricky functionality or use complex APIs (such as maps); (d) faster app composition.
- **Cross-concept queries**. In the current design, when a query must access the databases of multiple concepts, the sync code makes action calls to the concepts and essentially aggregates results. This is not ideal, both because it complicates the sync code and because it requires complex view actions in concepts (such as getByIds). Not clear how to fix this.
- **Request structure in syncs**. Some routes require fairly elaborate structure, in having multiple params and in packaging results into structured objects. This is currently handled by requiring the user to write prompts that contain this information. It could be mitigated by some metaprompts that provide general guidance on how request results should be packaged. It could also be absorbed inside standard BFF components (see below).
- **Frontend code**. Barish designed a nice DSL for frontend code which makes it easy to build a reactive tree that interleaves components from different concepts. He has also had some success using GPT to generate the DSL code. I am convinced, however, that the base functionality of Kodless should offer reusable frontend components.

# New Architecture

The proposed architecture offers reusable frontend components so that apps can be built without any frontend code generation.

Some elements of the new architecture might be:

- **Per-concept BFF**. Each concept comes with not only its backend component encapsulating a data store and actions, but also a default frontend component, perhaps implemented in the Backend for Frontend pattern. This new component is a server-side component that sits between the frontend and the backend like a proxy, and offers actions that respond with frontend widgets (in which calls to other component actions are embedded). 
- **Global CSS**. The frontend components use a standardized set of CSS styles to make it easy to achieve a uniform appearance, and to change it.
- **Default syncs**. By default, each action of a backend concept has an associated sync that simply wraps the action in a route. The frontend components don’t call the concept actions directly, but instead call the syncs. [Should these syncs be plain function calls instead of routes, so that the BFF components encapsulate all HTTP-related functionality?]
- **Overriding syncs**. When more application-specific behavior is needed, the default syncs can be replaced by specialized syncs, for example, adding authentication actions.

# Examples
