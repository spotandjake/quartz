---
publish: "true"
tags:
  - snow
  - programming
---

* Source: https://github.com/spotandjake/snow

Snow is a newer project I am working on and is still mostly in the idea phase, I noticed while working on my [[NixOS-Configuration]] that while the promises and benefits of Nix are truly amazing a lot of the issues surrounding it stem from the language having terrible syntax. The original goal for snow was just to provide a simple source to source language to make Nix easier to use but the goals have since shifted a lot. My current plan for snow is to serve a multitude of services in a single and easy to use api. The initial heart of Snow will be Snow-Lang a source to source language that compiles to Nix and hopes to improve on some of its short comings. As Snow is in control of your nix configs and describes what you are going to be doing, I am going to snow tooling  to create the snow manager which is a full system and project manager you write a simple `snow.toml` file describing `tasks`, `libraries` and `dependencies` with easy escape hatches and `snow` serves as a single cli and configuration for managing everything, this can be combined with a rich language level templating system to provide amazing developer experience for project management. The nix model itself has issues but they are side effects of trying to package the years of tech debt over the various applications it manages, the future of snow is going to be wasm based, the entire compiler is being built using cutting edge wasm components from both grain and rust and eventually the goal will be to ditch the nix backend and install packages with wasm directly. 