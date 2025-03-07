---
publish: "true"
tags:
  - brisk
---
> [!NOTE]
> This is not a finalized plan and there are many issues I glossed over here, this was not originally written for publish so this work is unpolished.

In brisk we avoid files at all cost the entire compiler and language takes this to heart from the ground up, compilation in brisk works as follow: In brisk there are two higher level forms of code unit, `component`s and `module`s.

## Component
Components are like packages in a traditional language they can include files and contain other components or modules. They are linked at the binary level
```
component Main {
	print("Hello World")
}
```
## Modules
Modules in grains are like traditional files, they are linked internally from statements in modules directly inline the module in teh parent component and insert a use statement below. Modules can contain submodules and linking is done on the source ast itself 

## Source Linking
Components are really for external code linking whereas modules are for internal code linking. Source linking is normally a terrible idea because it doesn't scale way to large programs as you create a massive AST with no room for incremental compilation. Brisk solves this with a lazy AST and multiple parsing phases. The first parse phase gets just enough info for linking and then links the AST, unparsed elements in the AST are seen as raw/lazy elements and will only be parsed when they are consumed. We identify all components and modules and compile them in parallel using our task system allowing for deep incremental and parallel compilation across the entire toolchain.


## Lowering compilation
Brisk has tow levels of ir on the frontend a lower ir which is similar to native wasm and a higher level ir, during compilation we represent everything in the ast and compile higher order primitives into our lower level ir, this means any optimization for the language will affect all primitives by default, simplifies code gen greatly and helps with debugging the source can be expected in brisk itself. The outcome of this is basically every.