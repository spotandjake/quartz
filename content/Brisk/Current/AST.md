---
publish: "true"
tags:
  - brisk
---

> [!NOTE]
> This is a work in progress document and likely is not reflective of the current plan.

Care needs to be put into our lazy ast points I need to consider how we expand parsing files, for example as our language has side effects every time a function is called we need to parse all of the values anyways. On the other hand if we know a function isn't called we have no need to parse it except in the case in which we need to validate the entire program.
## Main AST
```rust
record Program {
	uuid: ...,
	name: String,
	dependencyGraph: Graph.t
}
enum CodeUnit {
	Component{
		uuid: ...,
		name: Ident.t,
		signature: Type,
		// Might need to represent imports and exports too
		body: List<LoweredExpression>,
		sourceLocation: Location.t
	},
	Module{
		uuid: ...,
		name: Ident.t,
		signature: Type,
		body: List<LoweredExpression>,
		// Might need to represent imports and exports too
		sourceLocation: Location.t
	},
}
enum TopLevelExpression {
	IncludeStatement{
		source: String,
		includeName: Ident.t,
		aliasName: Option<Ident.t>
	},
	Expression(Expression)
}
enum Expression {
	// TODO: Make function body lazy
}
```

## Lowered AST
```rust
enum LoweredCodeUnit {
	Component{
		uuid: ...,
		name: Ident.t,
		signature: Type,
		// Might need to represent imports and exports too
		body: List<LoweredExpression>,
		sourceLocation: Location.t
	},
	Module{
		uuid: ...,
		name: Ident.t,
		signature: Type,
		body: List<LoweredExpression>,
		// Might need to represent imports and exports too
		sourceLocation: Location.t
	},
}
// Refine these to the optimal level of compilation, should be things close to wasm semantics bassically like a syntax and type checking addition to wasm
enum LoweredExpression {
	MatchStatement{ ... },
	Function{ }, // ...no closure handling
	Application{ },
	Bind{ },
	Block{ }
}
```

When we handle evaluation over our ast we are trying to be lazy, so I need to investigate the information we will be carrying a bit more but my current idea is we collect as much simple info about bindings as we can and then iterate down through our functions in parallel where possible for example we can likely leave compilation of a function to be done in parallel and then handle the type checking constraints after in relation to each other