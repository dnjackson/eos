---
title: "Building on good ideas"
weight: -80
---
Concept design builds on many familiar ideas. To mention a few:
- From **use cases and user stories**, concept design takes the idea that software interacts with the world through scenarios that deliver value.
- From **user-centered design** (as taught by Don Norman in *The Design of Everyday Things*), concept design takes the idea that the user interface is a kind of apparition (a “system image” in Norman’s phrasing) that stands between the user and the system, revealing only indirectly the underlying reality. When the user’s understanding (their “mental model”) is not aligned with this reality (the “conceptual model”), the system becomes unusable.
- From **domain-driven design** (and its predecessors, such as OMT and JSD), concept design takes the idea that the most stable part of a system’s function may be grounded in structures that already exist in the problem domain.

In each of these cases, concept design goes in new directions. Unlike use cases, the scenarios of concept design are not used as specifications. Whereas user-centered design focuses on building the system image that connects the conceptual model to the mental model, concept design focuses on the conceptual model itself. And while domain-driven design looks for existing structures in data, concept design looks for them in function.

More fundamentally, concept design is about making the function of software *modular*: that is, dividing it into separable chunks that can be understood independently. Modularity is the key to many good things: separation of concerns, division of labor, reuse, decoupled implementation, etc. 

To achieve modularity, concept design draws on well-established ideas from computer science (in particular the idea of defining actions over states, and composing processes by synchronizing actions).

What about objects and classes? Given objects’ success in programming, and their use in simulations for modeling domains, you might have expected them to be the right modularity mechanism for concepts too. But as we’ll see, objects aren’t a good fit for structuring function.