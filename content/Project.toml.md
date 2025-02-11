---
publish: "true"
---

```ts
type Semvar = String;
type Dependency = { [key: string]: any | undefined };

interface ProjectFile {
	Name: String;
	Version: Semvar; // What is the purpose

	Tasks: List<unknown>; // TODO: Inspo from task manager

	Dependencies: List<Dependency>;
}
```

What does this solve:
* Dependency management
* Reproducibility control
* Common configurations

Goals:
* Project details
	* name
	* version
	* supported systems
* env vars??
	* project.toml contains an env spec
	* and then we require loading a matching `.env`
* task system
	* fully integrated task system
	* handles concurrency (Think about ci concurrency)
		* Multiple workflows across multiple servers?
* Dependencies
	* How can we unify this more???
	* nix backend???
* workflow generation
	* generating workflows (non platform specific)
		* Templating system?????
	* handling concurrency models
* Sbom generation (software bill of material)
	* We have a dependency list it wouldn't be too hard to generate a full sbom which is a good practice for securityy 
* Attestation integration
	* We have a full project overview we can look into just setting this up automatically for people
	
Future Opportunities:
* We have a schema
	* Wouldn't be too hard to build a tui or gui for configs
	* Wouldn't be too hard to give to an llm for generation
* Scanning for deps with an llm + combined failure catching?

CI Integrations:
* Task named test, will run on ci
	* automatically unless configured not too.
	* The moment you add a `test` task, we look at generating ci workflows that run it.
* 