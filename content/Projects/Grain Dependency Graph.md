---
publish: "true"
tags:
  - "#grain"
  - "#programming"
---
## Description
* Source: https://github.com/spotandjake/grainDepGraph

This library provides a simple dependency graph, that supports both cyclic and acyclic graphs. The module is based off the javascript library [jriecken/dependency-graph](https://github.com/jriecken/dependency-graph/tree/master).

Docs can be found in the `./depGraph.md` file and the library itself can be found in `./depGraph.gr`

# Example

In order to use this library copy the `./dependencyGraph.gr` file into your project and you can use it like a regular grain module.

```js
module Example

from "./dependencyGraph.gr" include DependencyGraph

let dependencyGraph = DependencyGraph.make() // Create a new dependency graph
DependencyGraph.setNode('A', 1, dependencyGraph) // Add a node with value 1
DependencyGraph.setNode('B', 2, dependencyGraph) // Add a node with value 2
DependencyGraph.setNode('C', 3, dependencyGraph) // Add a node with value 3
DependencyGraph.addDependency('A', 'B', dependencyGraph) // A depends on B
DependencyGraph.addDependency('A', 'C', dependencyGraph) // A depends on C as well
DependencyGraph.addDependency('B', 'C', dependencyGraph) // B depends on C
print(DependencyGraph.overallOrder(dependencyGraph)) // Ok(['C', 'B', 'A'])
```

# Contributing

Feel free to contribute additions to the library before making a commit run `bash publish.sh` or if you are on windows feel free to run the commands individually, to generate docs, format and test your additions.