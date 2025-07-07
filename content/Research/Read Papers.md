---
publish: "true"
---
This is a non exhaustive list of various papers I have read and some of my thoughts, summaries and takeaways.
## ​Course Experience Report: Full-Class Compiler Collaboration
* Link: https://jpolitz.github.io/docs/splash-e-2021.pdf
* Topics: Wasm, Compilers, Course Design
* Date: 2025-02-01

This paper analyzes the effectiveness of full class collaborative programming in UCSDs CSE231 Advanced compiler course. 
The start of the course has the students working individually on a compiler for CHOCOPY a subset of python, in the later part of the class groups were made and each group was assigned a feature to contribute to a collaborative compiler. The analysis of the class showed that most objectives were successful with most issues coming from time constraints, and upfront planning issues for the first iteration of the course.

## An Incremental Approach to Compiler Construction
* Link: http://scheme2006.cs.uchicago.edu/11-ghuloum.pdf
* Topics: Compilers
* Date: 2025-02-01

This paper is a tutorial on building a tiny scheme compiler, it proposes an incremental approach over staged a staged approach for writing compilers to bridge the gap between toy and production grade compilers.

## The Simple Essence of Algebraic Subtyping
* Link: https://infoscience.epfl.ch/server/api/core/bitstreams/afe084e0-0050-4542-99c7-c499d2fe1620/content
* Topics: Typechecking
* Date: 

I honestly need a refresh on this because its been a while since I read it but it is one of my favourite papers on typechecking do to its intuitive approach and defintion.

## Typing the Numeric Tower
* Link: https://users.cs.northwestern.edu/~stamourv/papers/numeric-tower.pdf
* Topics: Type checking, Numerics
* Date: 2025-05-11

This paper is describes the number system of typed racket, which allows for type safe and intuitive arithmetic between different number types a common challenge in many programming languages. The implementation uses union types with subtyping and a predefined tower to maintain strong static guarantees without runtime checks.