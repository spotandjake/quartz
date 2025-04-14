---
publish: "true"
tags:
  - compilation
  - programming
date: 2025-09-13
---
[A-normal form](https://en.wikipedia.org/wiki/A-normal_form) (ANF) is an intermediate representation (IR) commonly used in functional compilers to make optimizations easier. While the name might sound intimidating, it’s actually a rather simple transformation:  converting an expression so that all function arguments are either `values` or `binds` (i.e., trivial expressions).

Take this grain code as an example:
```js
get(3 * 4, set(5))
```
in ANF it becomes:
```js
$arg0 := 3 * 4
$arg1 := set(5)
get($arg0, $arg1)
```
This transformation is rather trivial to do it is essentially just go down your AST and when ever you hit something non trivial so an argument that doesn't follow the rules convert it into a bind in a `setup` for the function otherwise leave it, and then return the `setup` and `bind`.

This transformation is relatively straightforward. You recursively walk your AST, and whenever you encounter a non-trivial expression in a position that expects a trivial one (like a function argument), you extract it into a temporary variable (a `bind`) an ANF expression might look like `(list(bind), triv_expr)`, this can also be done through an expression stack.

## Purpose

ANF is useful both as a **code lowering pass** and as a way to simplify **optimizations**.
### Lowering

The constraints that define ANF align closely with the way we represent higher level code across low-level targets. For example:
* If you're targeting assembly, many instructions only accept simple (immediate arguments - similar to ANF)
* If your targeting WebAssembly (a stack machine), these temporary bindings can be removed during codegen and you are left with your stack order. 
### Optimizations

ANF helps by making all analysis for most optimization passes far more local. Instead of having to deeply recurse over the nested expressions of a traditional AST if you write an optimization that says `1 + 2` is `1` it can be applied to every instance of that, combined with multiple optimization passes from simple rules you unlock a lot of possible analysis and optimization opportunities. For example:
* **Constant Propagation**: Which is the process of inlining variables bound to constants, is made easier as you can just eliminate any bind pointing directly to an immediate.
* **Common sub-expression elimination (CSE)**: if two binds compute the same thing and are pure you can re-use them `a + b`.
* There are many many more passes but I haven't had time to learn all of them yet.

### Alternatives

> [!NOTE]
> I am far more knowledgeable about ANF than alternatives.

While ANF is one of the common options, it's not the only IR out there. Here is a quick breakdown.
* ANF
	* **Strengths**: It is really simple to implement and produces very readable codegen.
	* **Best for**: It works best with functional languages (purity) and reasonably well on imperative languages especially with a good purity system.
* **CPS (Continuation-Passing Style)
	* **Note**: My understanding of CPS is mostly theoretical I haven't worked much with it hands on.
	* **Strengths**: This makes control flow and effects extremely explicit, which is useful in theoretical work and advanced optimizations, though it hasn’t been shown to be more powerful than ANF in practice. CPS is especially effective if you are compiling to a lambda calculus as it follow similar restrictions.
	* **Tradeoffs**: More complex to work with and generate traditional code from.
* **SSA (Static Single Assignment)**
	* **Note**: I am far less knowledgeable about SSA as of the time of writing this so I may be wrong on some things.
	* **Strengths**: SSA is extremely popular in production compilers (like LLVM) because it makes dataflow explicit and enables aggressive optimizations like register allocation, dominance analysis, and dead code elimination.
	* **Tradeoffs**: It can be a slightly more involved process to transform into but it enables extremely powerful optimizations, like value numbering and register allocation.

Each of these IRs excels in different environments and with different target and source language so there is no `best` choice across the board however `ANF` fits a wide variety of resources and is a great entry to middle end optimization.

## Remarks

While middle end IRs can sound like daunting topics and rather confusing when you remove all the large words they are just simplified AST that make accessing certain information easier, the reason we use these common formats and there are different ones is the information available might make certain analysis or transformations easier.
### Attribution

Most of this knowledge comes from reading the [ANF Wikipedia page](https://en.wikipedia.org/wiki/A-normal_form) and working on the [Grain middle_end](https://github.com/grain-lang/grain/blob/2dba010ef640fbe821c45c690d28dd85e3016095/compiler/src/middle_end/analyze_purity.re), particularly around purity analysis and IR transformations.