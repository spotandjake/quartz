---
publish: "true"
date: 2025-01-28
---


TODO: add meeting date
TODO: trim down each talk
TODO: Organize Ideas
## Website
What would be like to put on a grain roadmap / status page, I think it would be best if we format this in a manner such as:
* Near Future
* Future
* Far Future
I was just wonering if we could take a few minutes to discuss what would go on this page, and in which sections and then we can design a page up and add it to the new website.
## Wasm GC
I wanted to discuss wasm-gc a little, earlier this week I was discussing a little with oscar on how we are going to represent our various different high level types under wasm-gc. I think it would be beneficial if we can determine an abi as we can start moving the runtime over to `struct.get` functions in the runtime and then replace those with the wasm instructions once we make the needed binaryen changes.
##### Abstraction
In october I made a thread in the ideas channel about abstracting access to our runtime primtives things like reading from an array would be done through `array.get` in the runtime rather than by manually loading the `wasm` address, this isn't really a change to codegen but just a matter of defining functions for all the low level stuff we are doing and then actually using those functions. This has many benefits it opens a few optimizations but most importantly it means the underlying data layout no longer matters we can support `wasm-gc`, `linear memory` even non wasm targets a lot easier as if you want the runtime datatype you would just call `GrainValue.getTag` implementation details would be hidden. This can be adopted incrementally, and if we combine it with the runtime pattern matching we can guarantee type safety even in the runtime.
##### Malloc Concern
We are still going to need to keep `malloc` around after `wasm-gc` for wasi interactions at least until component model and `wasi 2` can be adopted.
##### Proposed Data Layouts
* `$GrainValue` - The supertype of all structs on the gc level has a tag field
* Stack Types
	* I think these would get translated to `i31`s
* Byte like heap types
	* String - I think this would map to an array of `i32`
	* Bytes
		* I naively think this would map to an array but I think that's going to be slow.
	* Array
		* Maps to an array but we might need to figure out tagging semantics and how to resolve them.
* User Types
	* Record - A struct that is a subtype of `$GrainValue`
	* Variants
		* each adt would make a struct that is a subtype of `$GrainValue` 
		* each variant would make a struct thatis a subtype of the parent adt.
	* Tuples - A struct that is a subtype of `$GrainValue`
	* Box's - I think these would just map to `ref` types
* Polymorphism
	* Wasm-gc works through a subtyping system which isn't 100% compatible with grains type system.
	*  I think parameters will end up being `eq` types, all structs would be `GrainValue` types so we can inspect the tags the problem comes when you have an unbound `a` type that could be an `array`, `stack type` or `struct` I think the only way to disambiguate is using `ref.test` to `test` if you can cast to the given types after we know which type it is we can use tag information on the type to get the info.
##### Blockers
* Wasm GC in node runner
* Wasm GC in binaryen
---
* Node `v22`
	* Last week I got our js binaries building with a fork of `pkg` called `yao-pkg`, I think a good way forward here is if we just vendor `yao-pkg` to get the upgrade, were going to need a node upgrade for a few wasi things we are blocked on and wasm-gc work, we have been looking at node-sea but I think it would be easier to just vendor `yao-pkg` at the moment.
* Js Of Ocaml Upgrade
	* I am a little bit stuck on my js_of_ocaml stuff I mostly just need to find a bit of time to test on a windows machine, I am getting errors from our `cmake` patch in binaryen for some reason. 
	* The issues with `ocp-indent` came from a `symlink` that was happening in the tests, which I worked around by vendoring the library and just removing the test.
* Constants Cleanup
	* I started doing some constant cleanup throughout the stdlib and runtime here: https://github.com/grain-lang/grain/pull/2237 I started with the `String` module and plan todo other modules if we are onboard with the changes
	* Instead of me continuing with these changes though the wasm-gc refactor I was talking about above might be a better use of my time as its a pretty similar process of auditing the entire stdlib and runtime.
* Dedupe imports
	* The deduplicate imports pr I will try to get the requested changes you suggested oscar, Funnily enough I decided to use a binaryen pass because you had suggested that with the old linker. I think the semantics are pretty simple though, just ensure the types, name and namespace match, and then handle the resolved symbol.
* Grain-Stream
	* I have been working on a module mostly for my own use [here](https://github.com/spotandjake/grain-libs/blob/main/grain-stream/stream.gr) but I was wondering if it would make sense maybe to put into the stdlib. It's bassically just a library for advancing through a bytes object for parsing for example you can call things like `Stream.Get.char(char)` or `Stream.GetAndAdvance.char(char)` or `Stream.Expect.char(char)`
	* This is really useful for parsing both binary streams and text streams.
	* As an expansion I think this combined with some parser combinators would make parsing easy and standard in grain.
* Wit-bindgen
	* I think the pr's I have made to wit-bindgen are good to be merged.



* Silo
	* Could we repurpose issue `2000` for silo as its just sitting around.
	* I know that we wanted to wait to for the llbuild stuff to merge the initial silo pr but I was wondering if we would be open to accelerating it.
	* Silo is still useful before we have the llbuild stuff in, once we have silo we can use silo for project level lsp info, this would allow us to manage your project as a workspace, when you make changes in file `b` we could propogate them to file `a`, this also let's us take your compilation settings from `silo.toml` allowing standard imports between them.
* Grain popularity
	* After the contextfree video I think we all noticed that 2024 wasn't a big year for grain, we shipped a lot of new features but didn't enable a lot of new stuff.
	* I think when we launch the new website we could follow it with a few blog posts:
		* 0.7 launch post
			* we'll obviously have the 0.7 launch blog post which will cover, the new fs module, object files, general improvements.
		* State of the union
			* It's been a long time since the last grain state of the union but I think this might be a good time, we could highlight some of the major improvements we've shipped over the past few versions that enable new things and also point at some of the future stuff we plan on working on.
			* I think we could talk about component model here.
			* Wasm gc here.
			* Maybe thresher shoutout
			* Silo talk
			* Maybe reference some of the talks oscar has done on grain
			* all of this would hopefully get people excited and looking at grain a bit again.
		* Component Model Dom Preview (I already wrote a draft for wasmio)
			* Weather my talk get's accepted or not I think it would be a good idea to turn the demo into a blog post
			* We have been asked about grain dom quite a lot so being able to show that we are making headway in the area even if it is experimental is nice and will hopefully get people excited.
		* Grain library posts
			* I think it could be cool if we did some library demo's just small blog posts showing programs using some of the new grain libraries.
			* How to Json
				* Just a simple how to showing how to use json and lenses.
				* Demo how you might do structure validation
				* Demo modifying json.
				* Demo json parsing (Define an idomatic way of turning json into structured grain data)
			* How to FileSystem
				* The new FileSystem module is a big user experience change for grain, I have been vendoring the module myself since before we had the pr up and having easy io makes the language a lot more useful for scripting.
				* I'm not quite sure what we would want to make this on.