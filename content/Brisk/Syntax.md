---
publish: "true"
tags:
  - brisk
  - active
  - programming
  - compilation
---
This document mostly contains a basic overview showing some possible syntax for Brisk, this is a work in progress and more meant to give an idea on what the language will look like and is by no means complete, the first week of the course will focus on solidifying and writing out a proper grammar.

For reference or to get a feel for the language the [grain docs](https://grain-lang.org/docs/guide/basics) are a good reference as the syntax is very similar, brisk mostly tries to fix semantic problems with grain and take a different approach to type checking that allows for more clear programs, along with a completely ground up compiler architecture. 
## Module
**Grain Inspired**: https://grain-lang.org/docs/guide/modules
A module is a top level structure that contains code, you could almost compare it to a namespace in the classic c world, they can be used to organize code into logical units each file contains a module and modules can contain submodules.
```ocaml
module Main

provide let x = 1 // exposes Main.x

let y = 1 // not exposed

provide module SubModule { // Submodule Main.Submodule.
	provide let x = 1 // Main.Submodule.x
}
```

## From Syntax
**Grain Inspired**
A module can be included from a file into another file with the following syntax:
```js
from "<filepath>" include ModuleName // We state the name here so it's easy to anchor too
```
## Statements
### Control Flow
#### If
Standard c style if statements
```js
if (<expr>) { // Can also just be an expr
	<statementList>
} else { // optional else
	<statementList> 
}
```
### Loops
#### For
Brisk will implement loops with a standard c style syntax.
```js
for (<expr>; <expr>; <expr>) {
	<statementList>
}
```
#### While
Brisk will also implement standard while loops with a c style syntax though for v1 at least `do-while` loops will not be implemented.
```js
while (<expr>) {
	<statementList>
}
```
### Generic
#### Provide 
```
provide <statement> // This exposes a statement from a module
```

### Let
**Grain Inspired**: https://grain-lang.org/docs/guide/mutation
```rust
let x = 1 // x is now 1 and its immutable
let x = 2 // we just shadowed x, 


let mut y = 1 // we can change y now.

let rec z = [1, ...z] // z is recursively dependent on itself creating an infinite list

let rec f = (a) => a < 10 ? 1 + f(a) : 1 // recursive function
```


## Expressions

### DataTypes
#### Numbers
```js
// Numbers are generic I think I am going to use subtyping to handle differences between floats and ints but investigation needs to be done
1
0x1A
```
#### String
```
// Strings are similar to c
"str"
f"str ${<expr>}"
"""
Multiline String
"""
```

### Functions
Functions are first class and can be passed as if they were data, they can be recursive which is identifier by a `rec` keyword on the `let` binding.
```
() => <expr>

x => <expr>

(x, y) => <expr>

(x=, y) => <expr> // x is a named argument

(x=None, y) => <expr> // x is named but has a default

curry (x, y) => <expr> // can be applied partially

(x: Number): Number => x // x must be a number, and the func must return a number
```

### Lists
Lists are just syntax for linked lists, internally they are reprsented using adts
```
[1, 2, 3] // a list with three numeric items
[1, ...lst] // puts the contents of lst into the new list, can only be done at the end for perf reasons
```

### Arrays
```
[> 1, 2, 3] // Similar to list syntax but with `>` to mark them
[> 1, 2, ...arr] // array spread TBD on implementation details
```

### Records
TBD

### ADTS
TBD
### General
### Pattern Matching
**Grain Inspired**: https://grain-lang.org/docs/guide/pattern_matching
I don't know if this will hit v1 but compared to switch statements Brisk will have pattern matching which allows for rich pattern based destructuring
```js
match (x) { // tries to keep things exhaustive
	// if the input is Option<_>
	// then this matches the Some(value) case binding the value to x
	Some(x) => <expr>,
	_ => <expr> // default case
}
```
### Ternary
Similar to c style languages this is just an if statement for expressions:
```
<condition_expr> ? <true_expr> : <false_expr>
```

### Function Application
In brisk function's can be curried as in you can partially apply a function take the below code:
```
module Progarm
// a List submodule
module List {
	// List.map, takes a list and function and
	// calls the function with the list
	// curry keyword says we can apply this partially
	provide let rec map = curry (callback, data) => {
		// recursively using pattern matching to go through the list
		match (data) {
			[value, ...rest] => [callback(value), ...map(rest)],
			[] => []
		}
	}
}
// type for visual example
type addLastIter = (List<string>) => List<string>
// we only applied the callback so we now have a mapping function
let addLastNameIter: addLastIter = List.map((data) => data ++ " Last")
// we now apply the data and call the function because all params are filled
assert addLastNameIter(["Jake", "Brian"]) == ["Jake Last", "Brian Last"]
```