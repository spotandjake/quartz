---
publish: "true"
date: 2025-09-09
grainVersion: v0.7.1 / Active Preview
Author: Spotandjake
---
In Grain, there are two main types of conditional statements: **`if`** and **`match`**. These allow you to make decisions based on values or the structure of your data.

## If

The `if` statement works like in most languages, and **`else` is optional**:
```gr
let condition = true

if (condition) print("Condition is true")
```
You can also chain `else if` clauses:

```gr
let a = 5
if (a > 10) print("Greater than 10")
else if (a > 3) print("Greater than 3")
else print("3 or less")`
```
When using `if` as an **expression** that returns a value, **every branch must return a value of the same type**:
```gr
let value = if (true) 1 else 2
print(value) // 1
```
Failing to provide an `else` in this context would result in a type error, because the `if` expression wouldn't be guaranteed to produce a value.

> NOTE: This also allows `if` to fill the role of ternaries in other languages.

## Match
Pattern matching lets you branch on both **values and data structures**.

### Simple Value Matching
A simple `match` statement is extremely similar to a `switch` in other languages:
```gr
let x = 2

match (x) {
  1 => print("One"),
  2 => print("Two"),
  _ => print("Other")
}
```

### Matching Patterns

Even with simple values and lists, `match` can handle **complex branching logic cleanly**. Unlike `if` statements, which require multiple nested conditions, `match` lets you clearly separate each case.

For example, you can mix **different conditions on lists** in one statement:
```gr
let input = [1, 2, 3]

match (input) {
  [1, _, _] => print("Starts with 1"), // Matchs the first valid branch.
  [_, 2, _] => print("Second element is 2"),
  [_, _, 3] => print("Ends with 3"),
  _ => print("Other pattern")
}
```
Here, a single `match` expression checks multiple aspects of a **concrete array** without nested `if` statements. This makes your code easier to read, maintain, and reason about as your program grows.

> Tip: Later, with [Patterns](../DeepDives/Patterns), you’ll be able to destructure more complex data structures concisely—turning `match` into one of the most expressive tools in Grain.