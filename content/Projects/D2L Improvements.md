---
publish: "true"
tags:
  - programming
---

* Source: https://github.com/spotandjake/D2l-Improvements

Unfortunately this project is mostly archived as I no longer use D2L and some early design decisions along with bad api documentation led to it being partially implemted specifically for the D2L of the DDSB. This project is unique as most of the work was in api reverse engineering but it also involved a lot of programming work, it uses a custom built injector for Next.js injected to overwrite the page in order to avoid CORS and completely reskin the D2L client to make it more like google Classroom. The hardest parts of this project were both getting Next.js injected properly on a webpage while ensuring consistent shutdown of the scripts already running on said page, I do this by injecting an insanely small JS module as soon as the page loads that stops all running scripts, erases the Dom, cancels all requests and then parses the loaded html to allow scripts to work. The second hardest part was getting various different file types to show up in usable and near native previews on the web without a server.