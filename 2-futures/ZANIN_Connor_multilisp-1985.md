# Multilisp: A Language for Concurrent Symbolic Computation

## Summary

## Outline

### 1: Introduction

This paper introduces Multilisp, a programming language derived from Scheme.
Like Scheme, Multilisp follows a "largely functional style of programming."
Multilisp differs from Scheme in that it provides a set of constructs for providing concurrency.

The only complaint I have about the intro is in paragraph 5: "... existing Multilisp programs make only minor use of side effects."
This is a red flag because so called "side effects" mentioned when talking about functional programming represent a large challenge of distributed computing.
A language designed to implement concurrent programs should be tested against programs that make use of shared state.

### 2: Issues in Parallel Language Design

The sections begins by defining three binary characteristics of parallel languages, and four categories of languages that can be derived from them (it must be presumed the authors are holding the other four to ransom).


According to this paper, there are 4 challenges to consider when designing a parallel language.
* Software Engineering: the practicality of the language
* Numerical vs Symbolic computation: how the language treats both

There is also a section in the middle (2.3) that attacks the "CSP" programming paradigm.
This is not a general challenge for parallel language design but rather an address of the short-comings of one design paradigm in particular.

### 3: Something

### 4: Something

### 5: Dark Side

## Limitations and Extensions

## Editorial
