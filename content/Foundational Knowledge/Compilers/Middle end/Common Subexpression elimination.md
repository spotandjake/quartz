---
publish: "true"
tags:
  - compilation
  - programming
date: 2025-04-14
---
## Explanation

> [!NOTE]
> This explanation assumes we’re working with [A-normal form](https://en.wikipedia.org/wiki/A-normal_form) (ANF) as our intermediate representation (IR) but can be applied broadly to other IRs.

**Common Subexpression Elimination (CSE)** is an optimization technique used in compilers to reduce redundant computations and allocations. The idea is rather simple: if a program computes the same *pure* expression multiple times, we can compute it once and reuse the previous result.
## Goal

Our goal is to identify all _pure_, trivial expressions, and eliminate any duplicate computations by reusing the original binding. For example, take the expression `(a + b) * (a + b)` by default, this computes `a + b` twice. With CSE, we can lift the common subexpression into a temporary bind like so:
```js
$arg0 = a + b
$arg1 = $arg0
$arg0 * $arg1
```

This technique also applies to allocated values for example `None` or `"test"` as long as they are referentially transparent (immutable). 

## Transformation

Working in anf the transformation for this is pretty easily, we are going to scan through the anf expressions looking at each trivial expression, if the expression or value is pure we put it's bind in a table and then later if we see another expression we can just point to the previous bind.

In ANF, this transformation is rather straightforward. Since each intermediate result is already broken into a named binding (`bind`), we can:
* Walk through the `ANF` expression
* If the expression is **pure** (i.e., has no side effects):
* Check if we’ve already seen this expression in our table.
	* If so, reuse the previously bound variable.
	* If not, add the current bind to the table `Map<Expression, Bind>`.

This works because ANF makes it easy to identify trivial/pure expression and their scopes, while also simplifying expressions into trivial subexpressions we can identify globally.
## Notes
* You can be more aggressive with this optimization with better purity analysis.
	* For example a function might be marked as impure if it allocated temporary memory like an array to the heap, usually that side effect isn't the desired behaviour of the programmer, to make the most out of this you would need to track purity at the type level.
* This optimization has several benefits:
	* If your language is using structural equality  ADTs like `None` can require actually comparing the tags and values from memory of the ADT whereas if we are sharing the input they are more likely to have the same pointer allowing for a fast path to be hit and a quicker comparison.
	* This greatly reduces allocations especially with immutable data structures.
	* This reduces program size as it is deduplicating code.
* Some things to watch while implementing
	* Purity is key here if your functions are never pure (take object oriented) this won't gain as many benefits but if you are working with mostly pure functions this will replace a lot of code.
	* You need to ensure that your data type and functions are referentially transparent otherwise you will be modifying the wrong value.