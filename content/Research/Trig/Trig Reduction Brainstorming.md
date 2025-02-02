---
publish: "true"
tags:
  - programming
  - math
  - trig
---
This notes contains research I was doing on computing trigonometric functions for large values, the general idea was that as `sin` repeats over a period we could look for a fixed integer approximation of $n\pi$ and do a simple reduction of $sin(x) = sin(x-n\pi)$ in order to use more traditional techniques, the outcome of this research looked somewhat promising, the technique itself is valid however the integer given by $n\pi$ grows quickly which means that for this to be practical you would need a special way of storing the number in a more compressed form, this is all possible but I was working on other projects so I did not have time to build a program to search for these approximations and test them while looking into the storage optimization. The notes below are were written well researching and have not had much polish.
# Generation 1
This is a fascinating idea! You're essentially proposing to leverage a very precise rational approximation of π/2 (represented as c/n where c and n are integers) to perform argument reduction using integer arithmetic. This could potentially lead to faster and more accurate trigonometric calculations, particularly on systems where floating-point operations are expensive or undesirable.

Let's break down the concept and explore its feasibility:

**Understanding the Core Idea**

Your approach hinges on finding integers c and n such that `c/n ≈ π/2`. This approximation allows you to express multiples of π/2 as integers.

The key insight is that `sin(x - c*m) = sin(x)` for any integer m, provided that `c ≈ n*(π/2)`. This means you can effectively "wrap" the input `x` around multiples of `c` to reduce it to a range where your kernel approximation is accurate.

**Steps and Considerations**

1. **Finding the Best c and n:**
    
    - This is crucial for the accuracy of your method. You'll need to find a good rational approximation of π/2 that balances precision with the size of the integers involved. This might involve exploring continued fractions of π/2.
    - Continued fractions provide a sequence of increasingly accurate rational approximations for any real number. The larger the terms you use in the continued fraction, the more accurate your c/n will be, but the larger your c and n will become.
2. **Argument Reduction:**
    
    - Given your input `x`, you would calculate `m = floor(x/c)`. This gives you the integer multiple of `c` closest to `x`.
    - Then compute `x' = x - m*c`. This is your reduced argument. You'll now work with `x'` instead of `x`.
3. **Precision and Integer Overflow:**
    
    - The accuracy of your argument reduction depends on how well `c/n` approximates π/2. If `c/n` is not extremely close to π/2, errors will accumulate over many periods of the sine function.
    - If `c` and `n` become very large, you need to be careful about integer overflow. You might need to use big integer libraries, which could affect performance.
4. **Kernel Approximation:**
    
    - Once you have the reduced argument `x'`, you'd apply your chosen kernel approximation (e.g., Taylor series) to calculate sin(x'). You might need to scale `x'` by `1/n` to align with the domain of your kernel.
    - Choose a method whose accuracy matches the desired accuracy and whose complexity can be managed efficiently.

**Example (Conceptual)**

Let's say you find that `c = 157` and `n = 100` provides a reasonable approximation of π/2 (note: this is just for illustrative purposes and not very accurate).

If your input `x` is 1000:

1. **Argument Reduction:**
    
    - `m = floor(1000 / 157) = 6`
    - `x' = 1000 - 6 * 157 = 58`
2. **Kernel Approximation:**
    
    - You'd then use a suitable approximation method to compute `sin(58/100)`.

**Further Exploration**

- **Investigate Continued Fractions:** Research how to compute continued fraction expansions of π/2 and experiment with different levels of truncation to find a good balance between accuracy and integer size.
- **Error Analysis:** Carefully analyze how the error in your approximation of π/2 propagates through the argument reduction and kernel approximation steps.
- **Performance Benchmarking:** Compare the performance of your proposed method against traditional floating-point trigonometric functions.
- Specialized Bigint algorithms for reduction
- Specialized route for applyiing the reduction to floats using exponent relations.
- 

This is a promising avenue for exploring alternative ways to compute trigonometric functions, especially in contexts where integer arithmetic is preferred. Good luck with your experiments!

# Generation 2
This is a really interesting problem! You're essentially proposing a novel approach to argument reduction for trigonometric functions by leveraging integer approximations of multiples of pi/2. Let's break down your idea and discuss its potential, challenges, and error analysis.

**Understanding Your Approach**

Your core idea is to find integers `n` and `c` such that `n * (pi/2)` is very close to `c`. This lets you reduce the argument `x` of your sine function by subtracting `c` (which is effectively subtracting a multiple of pi/2, but using only integer operations). The aim is to reduce `x` to a range where your kernel function (like a Taylor series approximation) is highly accurate.

**Potential Benefits**

- **Integer Arithmetic:** By using integers for the reduction step, you potentially avoid floating-point errors introduced in traditional argument reduction techniques.
- **High Precision (Theoretically):** If you can find suitable `n` and `c` with a very small difference from `n * (pi/2)`, you could achieve very high precision in the reduction.

**Challenges and Considerations**

- **Finding Suitable `n` and `c`:** The crux of this approach is finding pairs of integers `n` and `c` that accurately approximate multiples of `pi/2`. This is essentially a Diophantine approximation problem, and finding very good approximations with huge `n` and `c` might be computationally expensive. You would need to explore algorithms like the continued fraction expansion of pi/2 to efficiently find these pairs.
    
- **Error Accumulation:** Even with very accurate `n` and `c`, there will be a residual error from the approximation `n * (pi/2) ≈ c`. This error will propagate through the subsequent calculations and needs to be carefully analyzed.
    
- **Overflow for Large Numbers:** Dealing with large integers `n` and `c` might lead to overflow issues, especially if you're working with fixed-size integer data types. You might need to use big integer libraries or carefully manage the ranges of your variables.
    

**Error Reduction Strategies**

1. **Higher Order Approximations:** Explore more accurate continued fraction convergents of pi/2 to find `n` and `c` with a smaller error. The more terms you include in the continued fraction, the better the approximation.
    
2. **Residual Error Compensation:** Calculate the residual error `r = n * (pi/2) - c` precisely (possibly using high-precision floating-point arithmetic). Then, use a correction term based on this residual error in your kernel function. For example, if your kernel is `sin(x)`, you could potentially use `sin(x - r)` for a more accurate result.
    
3. **Hybrid Approach:** Combine your integer-based reduction with a final floating-point refinement step. This could involve adjusting the reduced argument by a small floating-point value to compensate for any remaining error.
    

**Analyzing Precision for Float64**

To stay within 1 ULP (Unit in the Last Place) of a float64 value, the absolute error of your approximation needs to be less than about `2^-53 * |sin(x)|`.

The crucial step here is to estimate the error introduced by your approximation `n * (pi/2) ≈ c`. This error is roughly `|n * (pi/2) - c|`.

To determine the required size of `n` and `c`, you would need to:

1. **Choose a continued fraction convergent of pi/2** that provides a sufficiently accurate approximation (leading to an error smaller than the target 1 ULP).
2. **Analyze the error propagation:** Model how the error from the argument reduction affects the final result of your sine approximation. This will likely involve using Taylor series or other tools for error analysis.
3. **Iterate:** If the error is too large, choose a more accurate convergent of pi/2 (with larger `n` and `c`) and repeat the analysis.

**Further Exploration**

- **Existing Argument Reduction Algorithms:** Research techniques like Cody-Waite or Payne-Hanek argument reduction to compare your approach and potentially incorporate ideas from them.
- **Specialized Libraries:** Explore libraries that handle high-precision calculations (like GNU MPFR) if you need extremely accurate approximations.

This is a challenging problem with a lot of potential for optimization! It requires a good understanding of numerical analysis, continued fractions, and error propagation. Good luck with your explorations!


Very insightful discord discussion:
https://discord.com/channels/530598289813536771/627842350357413898/1303160478238769186
https://discord.com/channels/530598289813536771/627842350357413898/1303192625322655806

Handbook of Floating-Point Arithmetic by Muller.
crlibm and rlibm

https://en.wikipedia.org/wiki/Mil%C3%BC
* [Approximating pi to given accuracy](https://www.quora.com/What-is-the-best-approximation-of-number-PI-as-a-ratio-of-two-number-x-y0)
* [Approximation Algoirthm Research](https://en.wikipedia.org/wiki/Diophantine_approximation)
  ![[PiRationalAccuracy.png]]