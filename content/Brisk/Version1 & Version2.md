---
publish: "true"
tags:
  - brisk
date: 2025-04-14
---
Version 1 and Version 2 were when Brisk actually started to qualify as a real language. After taking a year or so away from my interest with programming language design, discovering the young Grain language restored my interest and I decided to write a language that took after it and compiled from scratch to webassembly.

I group the two versions together as v1 was almost functional but I did a complete rewrite to simplify code leaving me with v2 a half working compiler with no type checking but good polymorphic runtime code, you could actually write hello world though you have to write to memory manually as we cannot allocate strings just yet.

I learnt a lot from this implementation about compilers but I was still very new and without any proper guidence I made a lot of bad mistakes.