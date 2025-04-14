---
publish: "true"
tags:
  - grain
  - programming
---
## Description
* Source: https://github.com/spotandjake/GrainPrint

This is an alternative library for printing values in grain, it is very similar to and in fact based upon the internal grain print but adds colors and better line breaking semantics while it is much slower than the internal grain print it is extremely useful for visualizing large dataStructures, as such I tend to use it a lot while developing my other grain libraries.


There have been talks about adding this functionality to the regular grain print function however it isn't quite that simple as Wasi doesn't tell the Wasm sandbox if it is a TTY or not, this library deals with the problem by ignoring it but if it were used in a non TTY environment it would end up adding a bunch of ascii codes to the output which wouldn't be desired. 