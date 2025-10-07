---
publish: "true"
date: 2025-10-06
grainVersion: v0.7.1 / Active Preview
Author: Spotandjake
---
# Variables in Grain

In Grain, variables are defined using the `let` statement, as shown below:
```gr
let str = "Hello World"
print(str)
```
These variables can then be referenced later in your code.

---
## Patterns

Patterns allow you to destructure and bind data. Some common examples are shown below:

### Tuple
```gr
let (a, b) = (1, 2)
```

### List
```gr
let [first, ...rest] = [1, 2, 3]
```


A more detailed description of patterns can be found [here](../DeepDives/Patterns)

---
## Recursion

Sometimes data in Grain need to be recursive. Currently, Grain only supports **recursive functions**, not recursive values. Here’s the syntax:
```gr
let rec loop = () => {
	loop() // Calls itself
}
```

Without the `rec` keyword, the compiler will raise an error to help prevent unintentional recursion.
### Mutual Recursion
In more complex cases, you might need **mutual recursion**, where two or more functions call each other. This can be done with the `and` keyword:
```gr
let rec greet = (times) => {
  match (times) {
    0 => print("[Grain]: Conversation Over!"),
    _ => {
      print("[Grain]: Hello!")
      reply(times - 1) // recursive call
    }
  }
} and reply = (times) => {
  print("[User]: Hi there!")
  greet(times) // recursive call
}
```

When run with `greet(2)` this produces the output:
```
[Grain]: Hello!  
[User]: Hi there!  
[Grain]: Hello!  
[User]: Hi there!  
[Grain]: Conversation Over!
```

---
## Mutable Variables
Sometimes you may want to make a variable mutable—allowing you to change its contents later. This can be done with the `mut` keyword:
```gr
let mut x = 1
print(x) // 1
x = 2
print(x) // 2
```

---

## Shadowing
A more idiomatic way to write Grain is to avoid mutable variables. This is common in functional programming but can lead to naming patterns like `x`, `x2`, `x3`, or `data`, `newData`.  Grain solves this through **name shadowing**, allowing you to redefine a variable in the same scope:
```gr
let data = 1
print(data) // 1
let data = 2
print(data) // 2
```

Although the example above looks like it mutates `data`, it actually defines a **new variable**, potentially with a different type. This keeps names simple and helps avoid state-related bugs such as accidentally referencing old states like `data` instead of `newData`.