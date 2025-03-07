---
publish: "true"
tags:
  - brisk
---
> [!NOTE]
> This is not a finalized plan and there are many issues I glossed over here, this was not originally written for publish so this work is unpolished.

A common problem with compilers is bad semantics around dealing with files in imports. Brisk solves this by only acknowledging the file system once during the first `include` parse scanning phase, after this everything in the compiler is content addressed. The language and compiler have been designed from the ground up to take advantage of fast and lazy compilation. In brisk there are two main code units for organization. Components and modules, both have different goals and should be used for different purposes.

## Component
Components are like packages in a traditional language they can include files and contain other components or modules. They are linked at the binary level
```
component Main {
	print("Hello World")
}
```
## Modules
Modules in grains are like traditional files, they are linked internally from statements in modules directly inline the module in the parent component and insert a use statement below. Modules can contain submodules and linking is done on the source ast itself 

## Lazy AST
In general the ast is lazy and parsed as things are needed, in other words we have a `Raw` node in the ast and only parse anything that would affect code gen, so unused functions or modules will never need to be parsed. Additionally we compile things in isolation as separate tasks allowing for parallel optimizations in the future, after we compile each module my plan is to use a graph of the remaining unresolved types to resolve the type checking for the entire program. 

## Source Linking
Components are really for external code linking whereas modules are for internal code linking. Source linking is normally a terrible idea because it doesn't scale way to large programs as you create a massive AST with no room for incremental compilation. Brisk solves this with a lazy AST and multiple parsing phases. The first parse phase gets just enough info for linking and then links the AST, unparsed elements in the AST are seen as raw/lazy elements and will only be parsed when they are consumed. We identify all components and modules and compile them in parallel using our task system allowing for deep incremental and parallel compilation across the entire toolchain.

## Lowering compilation
Brisk has tow levels of ir on the frontend a lower ir which is similar to native wasm and a higher level ir, during compilation we represent everything in the ast and compile higher order primitives into our lower level ir, this means any optimization for the language will affect all primitives by default, simplifies code gen greatly and helps with debugging the source can be expected in brisk itself. The outcome of this is basically every.