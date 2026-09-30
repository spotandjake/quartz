---
publish: "true"
date: 2026-04-16
---
### `@yao-pkg/pkg`
I noticed that `@yao-pkg/pkg` had finally closed their [node sea issue](https://github.com/yao-pkg/pkg/issues/204). Currently we are vendoring `pkg` to avoid taking on a security risk of dependency injection through the patched js binaries which means whenever we want to update our node version we need to resync all of our vendored modules and do releases. If we could switch to using `sea` based builds we wouldn't need to vendor the library as it uses the official node.js binaries. 

The main motivations of this work are:
* Faster node.js updates
* No more vendoring
* More secure binaries
* We also get windows code signing from this

I started looking into this a little bit yesterday, with a decent bit of success, the main issues I found are below:
* `process.execPath` points to the bundled app (Solved)
	* We shell out using `exec` to run the individual commands. This works when working with the cli in development or when compiled with pkg traditionally, we get the reference to `node` using `process.execPath` to ensure it's the same version.
	* Blaine had a great idea to work around this which is we add an `index.js` file and resolve the entry point from there. The compiler and runner actually work at this point but there are still a few issues related to bundling the stdlib
* `process.pkg.mount` no longer exists (Solved I think)
	* Our `pkg.js` workaround for handling compiling the file system broke, I think this is just a matter of reimplementing the work around if we feel the need to keep it however i'm pretty sure that oscars out of source build work has made the work around unnecessary given every object file should be emitted to the `target/` directory.
* `@stdlib/grain` gets bundled but `@stdlib/grain/runtime` fails to resolve (partially solved)
	* I was trying to figure this out for quite a while and it seems that the symlink resolutions happening due to npm workspaces is creating issues.
	* Workarounds
		* I found two work arounds one of them is we could just avoid the npm workspace all together. (This works i've tested it)
		* The second option here would be to bundle `../stdlib` instead of `@grain/stdlib` when building, I don't see any real issue with this in our setup.
			* I actually really like this approach as opposed to work around, it's really just a matter of if we are happy with that? (I'm not sure there is any signifigance to having the @grain/stdlib package)
	* I think this is a bug in `@yao-pkg` and their walker so I am going to try to produce a minimal version of this that I can open a bug report for.
* jsoo fails to find the files in the snapshot folder (Unsolved)
	* I think this is probably the biggest thing holding us back from getting this working, for some reason it seems that `jsoo` isn't resolving the snapshot folder the same way even though it uses fs internally, so I need todo a bit more digging into that. (If this ends up being an actual problem without a good workaround we could possibly just write the stdlib to the same target directory were using for object files?)


### `Stdlib` suggestion
Related to [the out of source builds pr #2384](https://github.com/grain-lang/grain/pull/2384) I was wondering how we felt about switching stdlib imports from `from "list" include List` to `from "@stdlib/list" include List`.
We've breifly discussed this change in the past in the context of having imports be like `@stdlib/list` but I don't think we've ever come to a firm stance on it, and ussually just table it for later as it's not a change that needs to be made.

The reason I bring it back up now is that there are three "resolution modes" in the out of source pr:
* Libraries (These would be `-I` or `-L` library includes)
* Projects (These would be actual grain projects that are being compiled)
* Stdlib (This would be the stdlib)
The stdlib mode works exactly like a library however it needs to be handled separtly because we resolve libraries as `<library>/<includepath>` whereas we currently resolve the stdlib like `<includepath>` and just determine if it's part of the stdlib or not.  If we switched to the explicit version `@stdlib/list` we could drop the separate handling of the stdlib. We don't have to use `@stdlib` exactly I just figured it looked nice, and the `@` makes it clear it's a package not a directory.