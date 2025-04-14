---
publish: "true"
---

A unified place for project configuration.


Pain Points in Project Management: 
* Collaboration
	* People don't have your dependencies
		* Wrong node
		* Cross project conflicts java 8 vs java 9
		* Legacy setups
* Separate formatting setup for everything
	* Prettier wants prettier.xxx
* Separate task management and cli system
* Distributed configs
	* Releases processes are not documented and spread out
		* We solve this by bringing things close to home
		* maybe look into cross project workflows / hooks????
* Single templating system
	* we can have a `project init`

Pain Points In CI:
* Setup
	* Each platform different configs
	* Different limitiations
	* Doesn't mirror your local workflow
* Debugging
	* Different machine state
	* Debugging at a distance (We bring the ci state local)
* features
	* We can handle attestations/code signing
	* We can handle some distrubition with nix
	* We can handle software boms