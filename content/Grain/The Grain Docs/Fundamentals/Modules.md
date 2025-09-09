---
publish: "true"
date: 2025-09-09
grainVersion: v0.7.1 / Active Preview
Author: Spotandjake
---
In Grain, the base unit of code is a **module**. Every file of Grain code you write contains a module and every line you write belongs to a module. For example a simple `Hello World` program:
```gr
module Main

print("Hello World 🌾")
```

A module is similar to a _namespace_ in other languages: it’s a static container for your code that helps you organize functionality into clean, logical units.

Modules, in grain serve many purposes:
* Naming - A module provides a fixed name (here `Main`). That name is used throughout the language to refer to the code inside it.
* Organization - Modules let you structure your program into smaller, well-defined units instead of having everything exist within a global scope or file. Think namespaces in other languages.
* Abstraction - Modules serve as a simple layer of abstraction, letting you keep certain things private and provide exports to other modules.

> **Tip:** It’s best practice to name a module after its file name.

## Submodules
Grain also allows modules to contain **submodules**, providing further separation and organization within a single module. For example, an outline of a basic `Http` library might look like this:
```gr
/** A simple library for working with HTTP. */
module Http

/** Utilities for working with HTTP requests. */
provide module Request {
  provide let make = () => void // Example content
}

/** Utilities for working with HTTP responses. */
provide module Response {
  provide let make = () => void // Example content
}
```

Submodules are best used for **grouping related functionality** within a single parent module. They are ideal for creating **sub-APIs** or organizing your code into logical sections. In the `Http` example above, `Http.Request` and `Http.Response` represent distinct concepts that are both part of and closely related to the larger `Http` module.

### Key points
* Submodules **do not replace the need for separate files** — use files for large, independent features, and submodules for logical subdivisions within a module.
* Submodules help **structure code** while keeping it inside a single logical unit. `Http.Request` and `Http.Response` both remain part of the `Http` module.
* Submodules can be nested as deeply as needed, but as a rule of thumb, nesting beyond two levels may indicate that the parent module is too broad.

## Exposing Functionality

Modules in Grain can make parts of their content accessible to other modules. This allows you to control what is visible or hidden to the outside world. Providing functionality is a key part of designing a module and building a clean codebase.

Grain supports two ways to provide functionality: **concrete definitions** and **abstract interfaces**. The following sections introduce each concept.
### Provide

In grain the `provide` keyword is used to provide a `value`, `type` or `submodule` to the outside world. Anything marked as `provide` is accessible to other modules through the parent. For example:
#### Providing Values
```gr
module Main

module Example {
  /* An exposed value */
  provide let visible = 2
  /* An internal value */
  let hidden = 1
}

print(Example.visible) // 2
print(Example.hidden) // Error: Unbound value hidden in module Example
```
#### Providing Types
```gr
module Main

module Example {
  /** An exposed type */
  provide type A = Void
}
let a: Example.A = void
```

[More on Types](./Types)
#### Providing Exceptions
```gr
module Main

module Example {
  /** An exposed exception */
  provide exception Failure(String)
}
throw Example.Failure("Fail")
```

[More on Exceptions](./DataTypes/Advanced#Exceptions)
#### Providing Submodules
```gr
module Main

module Example {
  /** An exposed submodule */
  provide module SubModule {
    provide let content = 1
  }
}
print(Example.SubModule.content) // 1
```

#### Providing After Definitions
You can also provide exports after their definition using this syntax:
```gr
module Main

module Example {
  // Definitions
  let value = 1
  type T = A
  exception E
  module M {
    provide let val = 1
  }
  // Deferred Provide
  provide {
    value,
    type T,
    exception E,
    module M
  }
}
```
### Abstract

Grain allows **abstract types**, which expose a type without revealing its implementation. This is useful for hiding internal details while still providing a usable interface.
```gr
module Main

module Example {
  provide type Value = Number
  abstract type Id = Number
  
  /** Creates a new value. */
  provide let makeValue = () => 1: Value
  /** Creates a new user id. */
  provide let makeId = () => 1: Id
}

print(Example.makeValue() + 1) // 2

// Error: This expression has type `Example.Id` but an expression was expected of type Number
print(Example.makeId() + 1)
```

Abstract types are used in the standard library to hide the implementation of `Map`, `Set`, and other data types. They also guarantee that values are created and used through the module interface, which is useful for things like IDs.
## Includes
Grain lets you include modules from other files to build libraries or split large projects. Use the `include` keyword to bring in a module:

```gr
// Code inside ./b.gr
module B

provide let value = 1

// Code inside ./c.gr
module C

provide let value = 2

// Code inside ./a.gr
module A

from "./b.gr" include B // Include a module by it's name
from "./c.gr" include C as Aliased / Use `as` to alias a module

print(B.value) // 1
print(Aliased.value) // 2
```


## Similar Foreign Concepts

| **Concept** | **Similarities**                                              | **Differences**                                                                                                                       |
| ----------- | ------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| Classes     | Both organize code while providing a method of encapsulation. | Modules have no runtime representation, cannot be instantiated, and do not form hierarchies; they are composed rather than inherited. |
| Namespaces  | Both act as static containers for code.                       | Modules exist in one place and cannot be extended later; namespaces can be reopened or extended in other files.                       |
