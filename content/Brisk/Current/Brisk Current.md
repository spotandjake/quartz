---
publish: "true"
tags:
  - brisk
date: 2025-04-14
---
> [!NOTE]
> I will do my best to ensure this information gets updated as its available but it may be out of date or disorganized when you are reading it these are my active notes, I just try to make them clean enough to publish.

This area documents the current plan for the next implementation of Brisk since the last version the amount of knowledge I have gained and my goals have changed significantly.  Brisk v3 is all about data flow within the compiler.

The language has been designed to work with a parrellelized task build system for a compiler. We have two higher level code units `Components` and `Modules`. `Components` Follow the component model spec and mirror their semantics whereas modules are internal to a component, we source link modules using a lazy compilation where each module is compiled individually and the ast is streamed together, this is kind of a hybrid approach between regular linking and not as the linking process is just syntax sugar, with lazy links so we are not generating a massive ast. 