---
publish: "true"
tags:
  - grain
  - component-model
  - wasm
---
> [!NOTE]
> This is a work in progress idea
 
Grain Component
- new executable to ship with the compiler
- challenges
	- recursion encoded as `lazy-{name}`resource
	- polymorphic types (best option is to warn and omit)
	- type parameters - case by case basis
- output wit signature from a module signature
	- first convert the typed signature into wit
		- `Note: It may be smarted to use an ast more reflective of our format`
		- Submodules
			- So the entry module needs to describe a world
			- I think submodules describe interfaces
		- signature items
			- types
				- variants -> `variant`
					- `Note: Recurssion lazyiness needed`
					- `singleton` -> `singleton`
						- `Note: Invesitage all singletons as flags`
					- `(a, b, ...rest)` -> `tuple<a, b, ...rest>`
					- `{ x, y, z }`
						- We need to generate a new record for these ones and refer to it through our first tuple param
				- records -> these are translated directly into records
				- open -> `Throw warning`
				- abstract -> `Resource?????`
			- values
				- `function`
				- `global`
		- type mappings
			- functions  
					`(a, b, c) => a` -> `func(a, b, c) -> a`  
					I don't know how we should handle labeled args here
			- builtins
				- `number` -> ?????
				- `Boolean` -> `bool`
				- `List<a>` -> `list<a>`
				- `bytes` -> `list<u8>`???
	- then use smart-doc to output the wit in a formatted manner
- generate an import based binding bridge
	- recursion don't implement the resource physically just use its encoding, when serializing and deserializing.
	- check wit-bindgen grain and compare outputs to see
	- use wasm-tools to validate + a jco test harness
	- converting basic values like number
	- packaging enums varaints
	- function calls
- `grain component wit lib.gr` -> generate wit signature file
- `grain component binding` -> generate our wrapper entry point
- `grain component compile` -> compile our wrapper with your lib as input.