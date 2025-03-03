---
publish: "true"
---
Currently we have a lot of package managers, but there is no language agnostic universal package manager. It would be interesting to make one.

Some Details:
* Written in grain
* Language Agnostic
	* Something makes a package manager language specific what is it???
	* This might just be for things like customizing hashing or configuring linking
	* Consider alternate linking styles
		* maybe we fetch the local dependency path from the language impl??
* Package Source Agnostic
	* We should implement loaders for packages
	* By default we will implement a github loader
	* We will also implement a local loader
* `packages.toml`???
* wasmtime rust host
	* git integration


```toml
name = "name"
version = "v1.0.0" # Using semver
is_package = true

[packages]
version = "v1.0.0"


[overrides]
example = "*" # Overrides all versions
example2 = "v1.0.0" # Overrides v1.0.0
```