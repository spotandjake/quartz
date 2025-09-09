---
publish: "true"
date: 2025-09-09
grainVersion: v0.7.1 / Active Preview
Author: Spotandjake
---

> [!NOTE]
> These documents are currently a work in progress and do not necessarily reflect the finished product. For up to date documentation on grain, please visit [grain-lang.org](https://grain-lang.org/docs/intro), ask in the discord or on Github.

## Purpose

The purpose of these documents is to provide a deeper explanation of the various parts, features and idioms of Grain than the introduction guide, while being less formal than the Grain reference. 

## Introduction
While there is no true start or end to these docs, you don't need to start with the first document and end at the last, I have tried to introduce new concepts sequentially where possible, unfortunately as things are co-dependent, new syntax will sometimes be introduced before it is described assuming the reader has a basic understanding of the fundamentals of programming. 

## Outline
1. Fundamentals
	1. [Modules](./Fundamentals/Modules)
	2. Conditionals
		1. If Statements
		2. Match Statements
	3. Definitions
		1. let
		2. from
		3. recursion
		4. Shadowing
	4. Functions
	5. Looping
	6. Mutation
	7. DataTypes
		1. Numbers
			1. Intro
			2. Number
			3. Int/Uint
			4. Float
			5. Rational
			6. BigInt
		2. General
			1. Void
			2. Bool
			3. String
			4. Char
			5. Bytes
			6. Option
			7. Result
			8. Range
		3. Iterables
			1. Array
			2. List
		4. Advanced
			1. ADT's
			2. Records
			3. Type Aliases
			4. Exceptions
	8. Types
2. Standard Library
3. Deep Dives
	1. Patterns
	2. Type Syntax
		1. Aliases
		2. Adts
		3. Abstract Types Behaviour
		4. Recursion
	3. DataType Syntax