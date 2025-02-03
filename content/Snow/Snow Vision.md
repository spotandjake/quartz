---
publish: "true"
tags:
  - "#active"
  - snow
---
### Goals
Simplify project management across the board with a single unified source of truth for easily reproducibility.

The original goal of snow was just to make writing systems configs easier by providing a nicer syntax for nix (bassically just a code formatter for a different language), this will be combined with a nicer experience for nix flakes which makes things easier the way we do this is by having a `snow.toml` which bassically will handle your project configuration and then from there you can link to `snow` files for your project this is accompanied at the root by a `nix.flake` generated from `snow.toml` along with this will come a host of usability improvments.

##### Places where nix can be improved
* simple template unification
	* `snow template <source>`
* errors
	* maybe look into using tvix as a backend
* unified environment
	* manage your entire project in a simple way

---
### Overview
All three of these programs
##### snow-manager
Snow manager handles all of the `silo.toml` side of things it builds the flake using the `snow` program and handles forward facing project unification.
##### snow
Snow is the backend project relating to the language and ast manipulation.

##### Snow packages effort
This will include writing packages in snow to make the environment easier or by wrapping existing packages such as providing a unified toml or yaml api for system configurations powered by nix.

---
### Use
commands
* snow-manager
	* `snow template <source>`
		* We can basically use flake templates where you take from github but I want to have a default template repo and you can have a local template repo
	*  `snow task <task>` 
		* I fear nix might be too slow for general use of tasks though tvix might be a solution to this another solution might just be to compile both to nix and taskfile (using the taskfile go package) for both integration of high level package management and packaged repo's
		* I was thinking of using taskfile for this but I think I can do better and have the a task and taskType and then have it relate to a nix builder like `buildRust` or `mkDerivation`
		* It might be smarter to just generate your project configuration in snow and handle tasks separately using packaging information as given.
		* snow might also be allowed to wrap tasks from other  configs though I don't love this idea, I think it would be preferred to just call build or what not with them.
	* `snow check`
		* checks your entire snow project
	* `snow build`
		* Using silo.toml will check your entire project
	* `snow lock`
		* will take all the inputs from your `snow.toml` and lock their `commits`
	* `snow snow`
		* commands related to the snow language
		* `compile <inputDir> <outputDir>`
			* this compiles everything in a an input directory into the output directory
			* being careful to adjust paths back to the base path somehow???
		* `snow transpile`
			* Converts a nix program into snow
		* `snow ast <input>` --out, --nix, --snow
			* This will give the ast for a file
		* `snow format`
			* This will format a snow program
		* `snow check`
			* checks a snow `<inputDir>` for linting and format
		* `snow run` <- in the future we might have a runner based off tvix

---

### Design
This is a future design idea completely subject to change and not reflective of current design.
* `cli`
	* a cli wrapping `snow_manager` and `snow` just providing some high level things
* `./snow_manager`
	* Snow manager written in grain using component model
	* exposes a component
	* Imports the snow project, possibly consumes the snow component need to see if that makes sense
* `./snow`
	* Snow written in grain using component model
	* exposes a component 
	* consumes the rnix component
	* a cli component, this will link to the snow component
* `./nix_parser`
	* This will expose `parse` which will target wit for our common ast

---
### Current State
Currently i am working on designing the language an intiail ast and getting inital rnix bindings I thinkI can move over to the other setup later.

---
### Language
I might add incremental type checking in the future for a better design.