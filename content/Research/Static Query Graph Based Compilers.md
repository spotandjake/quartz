---
publish: "true"
tags:
  - programming_language_design
  - compilation
date: 2025-04-21
---

```
Task(
  logic: ((cond, trueBranch, falseBranch) as inputs, env) =>
    visit(cond, (c) => c)
    |> predicate()
    |> visit(trueBranch)
    |> predicate((env, cond) => cond !== true)
    |> visit(falseBranch)
  inputs: (node) => (node.cond, node.trueBranch, node.falseBranch)
)

type Task<d, c> {
  logic: (inputs: a, env: b) => Graph<c>
  inputs: (value: d) => a
}
```

This seems to be a completely novel idea for static based task systems as a compiler. As can be seen above while the solution I propose isn't perfect yet questions about `predicate` and `notFalse` (which I think can just be another visitor), we should be able to convert this to a compile time static graph analyze it and generate a rather optimal task execution system without doing that I think at runtime we can just run this and get free scheduling and many benefits of advanced compilers like incremental compilation.


## TODO
* Finish a reference design of the task types
* Write a knowledge post on traditional compilers
	* Query based
	* File based
	* Alternatives?
* Write a knowledge post on my idea
* Try implementing my idea
* Write a knowledge post on the impl
* Write a research paper
* Look into getting published