---
publish: "true"
date: 2025-04-14
---

- Fork `pkg-fetch`
	- Enable Actions Runners
	- Follow the changes I have made on my branch on my fork
		- I'm waiting for my fork to get upstreamed
	- Change urls in `package.json`
	- Build Assets (with `newRelease`)
	- Update shas
	- Release
		- I think this requires a secret check the workflow
- Fork `pkg`
	- Enable Actions Runners
	- switch to using `@spotandjake/pkg-fetch`
		- Do a search because its in `package.json` and the tests and stuff.
	- Regenerate yarn
	- Test
	- Publish