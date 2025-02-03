---
publish: "true"
tags:
  - programming
---
## Description
* Source: https://github.com/spotandjake/SmallBasic-Gun-Game

This project holds a special place in my heart, it isn't terribly complex or impressive to most people but it took hours to build and I'm not quite sure anyone has built anything like it before. This project was built for my final project in grade 10, we were meant to build a 2D game but I wanted to go 3d, for those (most of you) who are not familiar with [Small Basic](https://smallbasic-publicwebsite.azurewebsites.net/) its a simple basic dialect by Microsoft designed to make programming easy to learn, think of it as a step up from scratch. Small basic is a terrible language though and I don't just mean basic sucks I mean the design is terrible arrays are serialized into strings and deserialed when you write to them, everything is a string and just parsed as what it looks like to add things this leads to terrible performance, beyond that the standard library sucks there are basically zero functions not even `String.split` and it turns out you can get race conditions as all variables are global and events are called from separate threads, this all combines for a terrible development experience. I persisted though and after about 4 months of heavy optimization got a 700 line program that can render 3d frames at 30fps, I was also able to demo networking but didn't include it as it dropped the fps down to 15 and because of the array serialization limitations double buffering would be impossible causing immense flicker. 

I am extremely proud of this project as despite being in a very minimal language with a lot of restrictions I was able to learn a lot about optimizing for a specific platform and still managed to implement an extremely complex cpu based raycast renderer that allowed for playable games. 