---
publish: "true"
tags:
  - programming
---
## Description
* Source: https://github.com/spotandjake/RayCastEngine-CSharp

This was a completely custom built game similar to wolfienstein but with proceduraly generated maps I built in grade 11 for the final project. The final project was to build a 2D game using windows forms, but I decided to implement a custom hand optimized raycast engine instead. This project took about 2 months most of which was spent optimizing, the tech stack I had to use for the class is not very optimal for what I wanted todo so I injected XNA through the windows forms api. I could have just used the XNA 3d renderer at this point but I wanted to learn more about rendering so I stuck with the 2D renderer and in order to get it running at a good frame rate I had to learn about various optimization techniques such as interlacing, multithreading and even some more obscure techniques surrounding cSharps garbage collector.