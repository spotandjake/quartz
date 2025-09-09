---
publish: "true"
tags:
  - grain
  - programming
---
## Description
* Source: https://github.com/spotandjake/Grain-Toml

This is a mostly complete TOML parser written in [[Projects/Grain/Grain]] using a recursive descent style parser. It covers most of the unreleased `v1.1.0` spec 
A mostly complete toml parser for the grain language. The parser supports the toml `v1.1.0` spec which has not been released yet and is still in motion but it is mostly backwards compatible with the `v1.0.0` spec and should be able to parse most toml files, as long as they don't include dates, and there is a bug with key deduplication in nested lists. 