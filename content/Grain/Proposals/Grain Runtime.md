---
publish: "true"
tags:
  - programming
  - grain
  - programming_language_design
date: 2025-01-28
---
# Problem
One problem I see in the grain runtime is our lack of management and the issues that come up with unsafe types such as:
* When we change things or add new things we need to maintain them throughout the stdlib without warning hence failUnsafe
* We cannot do low level optimizations such as changing the data layout without being warned of potential bugs and we cant optimize smaller things because we might break the runtime.
* We cannot properly guarantee memory safety
* We lose track of values and garbage collection in unsafe land
# Solution
I have a four part solution which is incremental and can wrap up a lot of runtime code neatly. I am going to work my way incrementally through additions we can add overtime to help secure the grain runtime, below is a top down overview:
**Before Diving In**
* Here on out reference will specifically mean we are modifying the value associated with the pointer grain uses pass by reference but we are trying to abstract away these semantics.
* [[#GrainValue]]
* [[#DataTypes]]
* [[#Bounded Memory Blocks]]
* [[#Reference Tracking]]
* [[#Liquid Types]]
## GrainValue
A grain value will be a new datatype in the compiler, it will be a safe type but designed for use only in the runtime. The goal of GrainValue is to provide a common trackable interface for grainValue writes and a standard module for checking things like tags this will open the door to a lot more security when we are working with reference as when you call load from memory on a grainValue we can specifically check your in the range of the value you have in debug mode and being able to track value memory writes lets us mark a value as `reference` or not in terms of use, the goal is to abstract away a lot of stuff we do by hand now into compiler intrinsics and the GrainValue runtime module, additionally this makes garbage collection easy as we can still track the managed value.
Below is a few notes followed by a proposed module api.
**Notes**:
* This is a work in progress
* I may include some // Note: comments with future notes after we add the incremented features
```js
module GrainValue

// GrainValue type note this just exists in the compiler it has no runtime meaning and just lets us treat the value differently
abstract type GrainValue<a.> // This is provided by compiler a is an open type

// Converts a value into a grainValue
GrainValue.fromGrain(a.) -> GrainValue<a>
// This converts a grainValue to a value
GrainValue.toGrain(GrainValue<a>) -> a
// Inspection
// Tag will either be an enum here or a number depending on what we want preferably an enum
GrainValue.getTag(a) -> Tag
// Allocations
GrainValue.allocateArray(length) // Same as now, note we cant infer the array type :(
GrainValue.allocateString(byteLength) // Same as now, note we cant infer the array type :(
// other Allocate Functions
GrainValue.fromInt32(WasmI32) // Same as now
// Heap
// Through adding 
Grainvalue.isPointer()
GrainValue.loadI32()
// Other load commands

// Unsafe
// Unsafe because it breaks inferenceing and you may not give us a valid grain value...
GrainValue.Unsafe.fromWasmI32(WasmI32) -> GrainValue<a>
Grainvalue.Unsafe.toWasmI32(Grainvalue<a>) -> WasmI32
GrainValue.Unsafe.fromUsize(Usize) -> Grainvalue<a>
Grainvalue.Unsafe.toUsize(Grainvalue<a>) -> Usize
```

# DataTypes
After we do the initial GrainValue changes we can implement the `DataTypes` library which will be a common set of low level operations we tend todo on datatypes the api will look something like below:
```js
module DataTypes

// Number
DataTypes.Number.getSubType() -> Tag // could be enum could be value
// ADT - note can throw exceptions if not an adt, we can do debug assert
DataTypes.Adt.getTypeID(GrainValue<a>) -> Usize
DataTypes.Adt.getVariantName(GrainValue<a>) -> String
// isTuple Variant
DataTypes.Adt.getVariantArity(GrainValue<a>) -> Usize
DataTypes.Adt.getVariantValue(GrainValue<a>) -> Usize
// TODO: Record adt values

// Array
DataTypes.Array.getLength(Grainvalue<Array<a>>) -> Usize
Datatypes.Array.getItem(GrainValue<Array<a>>) -> a
Datatypes.Array.setItem(Grainvalue<Array<a>>, a) -> GrainValue<Array<A>>
```
This api would provide better inferencing support from within the runtime and additionally it would let us avoid random memory writes directly now when we are ready on the reference side we can very easily see if this function calls `Array.setItem` a mutation is happening and its a reference. At this point our runtime is a lot more controlled also notably this works a lot better with wasm-gc because all of our writes are very controlled, and type safe. This also helps with garbage collection as we can safely see where your putting an item directly into an array, and this can still be all that unsafe code we love for performance just under a different name and type signature. 
Also of note is that now that all of our data is being accessed in a controlled manner we can easily change the data layout without affecting anything downstream we only need to audit the unsafe writes which are easy to find with search. This would let us make cool optimizations like more complex compilation models for such as having some adts on the stack and the likes.

## Bounded Memory Blocks
The next step in the puzzle is a little more invasive and wide sweeping but it would enable extremely good security, Wasm is considered secure from remote code execution as the functions are put in a separate memory space to linear memory, the problem is you could still cause remote code execution or even just in general unintended consequences by writing into the wrong memory and outside of the value you are working on, `GrainValue` and the `DataTypes` are a large step in the right direction as they try to lower the reliance on raw memory writes but they wont support every use case so how can we fix this. We can implemented bounded memories using the api below:
```js
module Memory

// not a record because its compact in memory layout
// defines a slice of memory
struct MemorySlice {
	size: Usize,
	// Doesn't have to be bytes but needs to be an aliased type so we can track writes into and apply bonuds checking to the size at runtime during debug builds with assertions
	data: Bytes 
}

// Malloc is now unsafe as it gives you an unbounded memory we only should be using this in extremely rare cases
Memory.unsafeMalloc(size) -> WasmI32 // This is the same as current Memory.malloc
// Bassically Memory.malloc but returns a MemorySlice
Memory.boundedMalloc(size) -> MemorySlice// This provides a bounded memory region
```

## Reference Tracking
On top of this entire system we can implement reference tracking after the old unsafe code is gone which will allow us to track mutations (we could later provide this in function signatures), but also this lets us have many optimizations for free such as we can better track escape analysis, we can tell if an object is immutable, we can better determine if code has side effects which allows for a lot of optimizations. My suggestion is todo this through the TypeChecker the same way we handle `mut` as a tag, there are some complicated questions here related to higher order functions like `List.forEach` if you have function like so below:
```
let incrArr = (ref arr: Array<a>) => {
  Array.mapi((ref a, i) => arr[i] = a + 1, arr)
}
```
how does the reference work there because we need the `ref` on the callback but we cant have it on the map signature, and we cant pass a ref into an open type just randomly because then how do we know if we it is a reference going forward, I think some investigation just needs to be done by me here because I am pretty sure this isn't an insanely hard problem.

## Liquid Types
Finally if we wanted to really ensure full security having a well managed runtime like this makes the application of Liquid types which is the idea of refinement types inferred through hindley milner, This would let us have high levels of flow through in the runtime about values in our liquidTypes at the itemized level which would allow for some wild and widespread optimizations due to variable constraints and way more. 