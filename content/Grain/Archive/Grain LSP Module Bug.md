---
publish: "true"
---

I was digging into this again and noticed that this is actually visible in the compiler with the below code:

The below code causes a `NOT_FOUND` error because of the `use C.` where `C` is an alias:
```gr
module Main

module A {
  provide module B {
    provide let b = 1
  }
}
use A.{ module B as C }
use C.{ b }
```


I'm not sure why this doesn't affect our binding code or resolution elsewhere though, but it indicates things arn't quite as consistent. I think this might be because `find_value` goes through a completely different code path then `find_module`, and in the compiler I think we erase `aliases` when they are used on the module level in other places.