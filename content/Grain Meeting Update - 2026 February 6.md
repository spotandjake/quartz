---
publish: "true"
date: 2026-02-06
---
## Notable Work
Just some slightly cooler things that I worked on the past two week:
* In [#2348](https://github.com/grain-lang/grain/pull/2348) I added support for raising multiple well-formedness errors through the cli.
	* ~~Do to the way I implemented the feature and the way we handle displaying errors in the lsp however we still only support displaying the last error through lsp diagnostics however I am hoping to improve this in the future. [Issue here](https://github.com/grain-lang/grain/issues/2353)~~
	* This change was already merged and will be out for 0.7.1
* In [#2354](https://github.com/grain-lang/grain/pull/2354), I added support for displaying multiple errors in the lsp this is a follow up pr to the above well-formedness errors pr. I don't love how I ended up handling tracking the errors but I think this is something that we are best to improve in the future as streamlining it now seems like it would require standardizing how we report errors a lot more then we currently do standardize.
	* This pr is waiting on reviews, but hopefully can be merged before 0.7.1.
 * In [#2350](https://github.com/grain-lang/grain/pull/2350) I fixed a bug where programs like `test(A.VariantA, VariantB)` would compile even though `VariantB` is module `A` as well.
	* What was happening here was if the type definition was something like `test: (a, a) => b` when we went to check the type of the second parameter we checked it against the expected resolved type of `a`. The main use case of this functionality is when working with records it helps to disambiguate which module the record comes from, however in this case it was creating a bug (this is actually expected ocaml behaviour). In order to fix this I avoid the type based lookup in the cases of constructors which also solves a very similar issue when it comes to pattern matching.
	* This pr is a breaking change so we won't merge this until 0.8
* In [#2355](https://github.com/grain-lang/grain/pull/2355) I made a fix to how we handle exhaustive checks on when patterns combined with inline record constructors which closes [#2311](https://github.com/grain-lang/grain/issues/2311).
	* The issue was in our `Conv` module we were not handling `Any` patterns correctly generated from exhaust when its an inline record which causes a different error to get raised and then ignored preventing the proper exhaustive checks. I think the `Conv` logic we currently use seems a bit fragile so we may want to consider improving that eventually, probably as part of thresher. 
	* This pr is waiting on reviews, but hopefully can be merged before 0.7.1.
* In [#2356](https://github.com/grain-lang/grain/pull/2356) I implemented a code action to add graindoc on let binds and submodules which closes [#2301](https://github.com/grain-lang/grain/issues/2301)
	* This pr isn't quite ready to be merged as it still needs tests though I was hoping I could get some reviews before I add those to make sure the feature works as we would like and get feedback on the implementation. 

## General
These are some more general things I worked on that probably are not as important:
* In [#2352](https://github.com/grain-lang/grain/pull/2352) I added a test for our base type metadata size closing [#1834](https://github.com/grain-lang/grain/issues/1834)
	* In order to keep this size stable when changes are made to the runtime or pervasives we check the basesize a module compiled in runtimeMode.
	* This is waiting on review.
* In [#2351](https://github.com/grain-lang/grain/pull/2351) I added a more informative and user friendly error when a user trys to override `_start` without using the `--use-start-section` flag. This closes [#2273](https://github.com/grain-lang/grain/issues/2273)
	* This is waiting on review.
* In [#2349](https://github.com/grain-lang/grain/pull/2349) I continued playing around with some code cleanup throughout the compiler.
	* I've noticed that a lot of failures in the lsp and uncaught excceptions in the compiler come from exceptions such as `NOT_FOUND`, I started refactoring the compiler to make better use of options and results so these errors don't bubble up and we properly handle cases where possible.
	* I'm not quite done but before I continued this work I wanted to get other people's thoughts on this change. This probably also makes the most sense to merge after we get the gc stuff through.
		* This work did get me thinking a little about how we could make identifiers safer as well. This would be a rather large change but it would be nice if we only passed around `IdentIDs` in our ast and kept a pool of identifiers this way if you have an `IdentID` we know that the `Identifier` exists in the env.
* I did a performance test on [#2280](https://github.com/grain-lang/grain/pull/2280) and it seems like the caching is beneficial so that pr should be good to merge. 

## Questions 
I also have a few questions on some existing issues that I wanted to ask to see if we could make some decisions.

* [#45 Webpack plugin](https://github.com/grain-lang/grain/issues/45)
	* I was thinking that we should close this issue as out of scope given we removed the js-runner so there wouldn't really be anything special about this, it seems like something the community would be better to support or we would be better to support in it's own repo.
	* How we could support users a bit here.
		* We've gotten a few questions about if we support the compiler in the browser, I was wondering if it may make sense to provide a small programatic api and version of the compiler we publish to npm.
		* Currently when people ask about browser support we say that it works but we don't really support it as a team or provide help with implementing the playground elsewhere however if we provided a package we could support our own playground and others. It probably wouldn't be overly difficult to handle this either. 
* [#238 Grain emacs](https://github.com/grain-lang/grain/issues/238)
	* Can we transfer the issue for grain emacs to the language-server-repo as support for this would just require creating an editor plugin now that the lsp is separate.
* [#182 rename grain-langauge-server](https://github.com/grain-lang/grain-language-server/issues/182)
	* I was wondering if we should rename the language server repo to something like editor `grain-editor-plugins` to be more reflective of it's scope. This may make the most sense todo when we release 0.8
* Related to [#2203](https://github.com/grain-lang/grain/issues/2203) I put an example of how I wanted to make the lsp a little more resilant on [this branch](https://github.com/spotandjake/grain/tree/spotandjake/lsp-resilancy)
	* The entire idea isn't to just ignore errors but display them in a nicer way that doesn't crash the lsp from one `Not_found`, we've had programs that compile fine crash the lsp before and this a case where things can get a little annoying without this change. 
* [#1361 index functions](https://github.com/grain-lang/grain/issues/1361)
	* I had a question related to our `forEach` vs `forEachi` style questions. I was wondering how we would feel about providing a `Pervasives.withIndex` function and deprecating our indexable functions, The implementation would be similar to the example below, as a note we would still need our `reducei` functions unless we want something like `withIndex2`, I think the benefit of implementing this change now is we then support indexable varaints of all our iterable functions and we can switch to using enumerators like we wanted to later however supporting those isn't something that will happen anytime soon given we probably want to implement them using stack switching and beyond that we would need to completely rework how indexing into datatypes work, especially with respect to raw access and lists.
```
provide let withIndex = (fn: (a, Number) => b) => {
  let mut index = 0
  value => {
    let result = fn(value, index)
    index += 1
    result
  }
}
```