---
publish: "true"
tags:
  - "#grain"
  - "#programming"
---
## Description
* Source: https://github.com/spotandjake/Reaper

A basic argument parsing library for [[Grain]] inspired by the extremely popular [minimist](https://www.npmjs.com/package/minimist) parser from the Node.js ecosystem.

## Usage

In order to use `Reaper` copy the `./reaper.gr` file into your project and use it like any other grain module, documentation can be found in `./reaper.md`.

An example of how the library can be used is below, and a more apparent example can be found in `./example.gr`.
Running the code below with `grain example.gr -- --verbose -h` will give the result:

```
Presence: ["a", "b", "help"]
Bools: [("verbose", true)]
Numbers: []
Ints: []
Strings: []
Multi: []
Rest: []
Passed Args: []
```

example.gr:

```rs
module ReaperExample

from "wasi/process" include Process
from "array" include Array
from "set" include Set
from "map" include Map
from "./reaper.gr" include Reaper

// Get the command line arguments
let args = match (Process.argv()) {
  // Trim the runner binary or file (Different per wasm runtime)
  Ok(args) => Array.slice(1, args),
  // Note: Most likely a wasi permissions error
  Err(_) => fail "Failed to read command line arguments",
}
// Parse the arguments
let options = Reaper.parse(
  aliases=[> ("h", "help")],
  presence=[> "help", "a", "b", "c"],
  bools=[> "verbose", "bool"],
  numbers=[> "float"],
  ints=[> "int"],
  strings=[> "string", "profile", "profile2"],
  multi=[> "multi"],
  args
)
// Print the parsed options
match (options) {
  // Note: The arguments were parsed successfully
  Ok(options) => {
    print("Presence: " ++ toString(Set.Immutable.toList(options.presence)))
    print("Bools: " ++ toString(Map.Immutable.toList(options.bools)))
    print("Numbers: " ++ toString(Map.Immutable.toList(options.numbers)))
    print("Ints: " ++ toString(Map.Immutable.toList(options.ints)))
    print("Strings: " ++ toString(Map.Immutable.toList(options.strings)))
    print("Multi: " ++ toString(Map.Immutable.toList(options.multi)))
    print("Rest: " ++ toString(options.rest))
    print("Passed Args: " ++ toString(options.passedArgs))
  },
  // Note: An unknown argument was given
  Err(err) => fail Reaper.toStringError(err),
}
```