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

The sections begins by defining three binary characteristics of parallel languages, and four categories of languages that can be derived from them (it may be assumed the authors are holding the other four to ransom).
The characteristics include:
* presence of explicit parallelism - does the programmer have control over parallelism?
* presence of side effects - does the language allow for state changes?
* presence of a shared memory model - is the entire address space visible to all parts of a program (or is it partitioned)?

The paper defines four categories of languages based on these characteristics.
Multilisp is defined as having (1) constructs for explicit parallelism, (2) side effects, and (3) a shared memory model.

According to this paper, there are 4 challenges to consider when designing a parallel language.
* Software Engineering: the practicality of the language
* Numerical vs Symbolic computation: how the language treats both

There is also a section in the middle (2.3) that attacks the "CSP" programming paradigm.
This is not a general challenge for parallel language design but rather an address of the short-comings of one design paradigm in particular.
From its description, CSP seems to resemble the message-passing style of programming.

### 3: Something

### 4: Something

### 5: Dark Side

## Limitations and Extensions

## Editorial
