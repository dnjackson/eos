
---
---

- **Strong concepts.** Having one or a few core concepts that are embodied in scenarios that deliver what the user needs, overcoming points of pain and friction. For Zoom, that’s the meeting concept.
- **Conceptual clarity.** The core concepts of an app have to be conveyed, through the UI and all explanatory material, with clarity and simplicity. It’s probably not possible to do this for a “dual use” app (like Hangouts) that offers multiple concepts (eg, *video call* and *meeting*) for the same function. User are also very sensitive to needless complications and distractions in a UI (in Skype’s case, spam and advertisements).
- **Robustness**. If the concept implementations are buggy or unreliable, or fail to scale sufficiently, it won’t matter that their design is good. Users will abandon them anyway. Skype suffered from a reputation for bugs and low reliability. 

Robustness might seem orthogonal to concept design, but it isn’t. Bugs arise because developers get confused about what they’re trying to do, so having fewer, simpler concepts leads to more robust code.

# 
diagrams for these scenarios
- labeling the painful bits: actions and states in old scenario. do as annotations. and use same ones on new scenario.
- whether all steps must be included (eg end of call?); generally would be good to show completion but may not be necessary.
- whether user actions not in concept included (send link, talk); yes, helpful to show in grey.
- whether have to resolve how call ends or host leave early; no, show one example and leave full range to SM.
- can i lift the meeting concept? yes, because create action is still allowed (but how to specify that link returned is unique? does this really doom the lifting? and even join has to find the object, so it’s really not so easy to lift; mahybe a story here about why objects aren’t good?)
