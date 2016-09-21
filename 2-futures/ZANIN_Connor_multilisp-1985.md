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
* presence of explicit parallelism: does the programmer have control over parallelism?
* presence of side effects: does the language allow for state changes?
* presence of a shared memory model: is the entire address space visible to all parts of a program (or is it partitioned)?

The paper defines four categories of languages based on these characteristics.
Multilisp falls into the category defined as having (1) constructs for explicit parallelism, (2) side effects, and (3) a shared memory model.

According to this paper, there are 4 challenges to consider when designing a parallel language.

#### 1. Software Engineering: the practicality of the language

Is the language (1) topology-agnostic, and (2) easy to use?. The answers to both of these questions need to be true of a language in order for a language to be adopted. If the language is not portable, or is too difficult to use for the creation of programs, the language will not be adopted.

A motivation behind language design is to provide software developers with a tool for creating programs that do useful work. This is because real programmers work for real companies that need to make real money. This is not a bad thing, but is a consideration that must be made when designing a language. Additionally, if a language is not portable it will quickly become irrelevent. Not only is a machine-specific language not portable to other current systems, it will not be portable to *future* systems. Hence, real companies that need to make real money will tell their real programmers to create programs in languages that can take advantage of ever-improving hardware.

If programmability was not a consideration, we would all program in assembly. I would argue it is the sole purpose of programming languages to provide programmability.

#### 2. Numerical vs Symbolic computation: how the language treats both

This section outlines two broad classes of computations. The relevant difference between numerical and symbolic computation is the ability of the compiler to recognize opportunities for parallelism. In the end, they adopt the MIMD paradigm for Multilisp as it allows them to encompass both styles of computation.

An example of how SIMD is inappropriate for symbolic computation is as follows. Imagine an array is to be sorted in parallel. As in the case of the parallel "sample sort" each thread handles a "bucket" of data. Initially, the data in the array is partitioned into buckets (course-grain sort). Then, the data is sorted per thread (fine-grain sort). If we only look at the latter (fine-grain) stage, it is easy to reason that not every thread will perform the same instructions on its local data. Each bucket of local data might have different relative orderings. In other words, MIMD is necessary because instructions are data dependent.

#### 3. Determinacy: does the language garuantee a program given the same inputs will produce the same outputs?

Because Multilisp supports explicit parallelism and side effects, it is nondeterministic. The authors accept this trade-off, as it allows programmers more flexibility when writing parallel program.

#### 4. Styles of parallelism: how will the programmer introduce parallelism to an otherwise sequential algorithm?

The authors state there are two ways of introducing parallelism into a sequential algorithm. The first is to perform the same set of operations, but recognize dependencies between operations to determine which branches of code can run concurrently. By this method, scheduling is not so much an issue because *all operations must be performed.* The second way of introducing parallelism is preemptive; tasks are scheduled which may or may not provide useful results. Tasks generated this way are more difficult to schedule, because they require a weighting mechinism to schedule tasks that are more likely to provide useful results more often.

Here, I would have appreciated a citation of such an algorithm that makes use of what they call "eager-beaver" concurrency.

Is this related?
https://groups.csail.mit.edu/cag/softspec/FDDO.pdf

In any case, they state that Multilisp does not currently (1985) speculative parallelism but it is a candidate for future work.

#### Criticism of CSP

There is also a section in the middle (2.3) that attacks the "CSP" programming paradigm.
This is not a general challenge for parallel language design but rather an address of the short-comings of one design paradigm in particular.

From its description, CSP seems to encompass the message-passing style of programming. An MPI program follows the CSP classification, in that an MPI program contains mechinisms for explicit parallelism, and when parallelism is present execution occurs at the process level; processes have independent address spaces. Further, MPI is built on top of C, a language that allows for so-called side effects. The only case against classifying MPI as a CSP language is that it allows for thread-level parallelism within the address space.

The author claims the "principle failings" of CSP are that (1) each domain of data is restricted to a sequential thread and that (2) this distinction of data locality leads to non-uniform access.

This paper is dated, so I may forgive it. I wonder how the authors would reflect on these words in the face of MPI? In the scientific computing and HPC community, the CSP-style language MPI is hugely popular. Particularly, it is popular *because* of (1) and (2). The size of problems has increased since the publication of this paper. Now, it is necessary to have concurrent programming languages that can cope with being run accross multiple machines. In the case of multiple machines, (1) is necessary (well, not if RDMA is used http://www.mcs.anl.gov/~thakur/papers/rma-perf.pdf, http://wgropp.cs.illinois.edu/courses/cs598-s16/lectures/lecture34.pdf). If (1) is necessary, then (2) is advisable in order to give programmers the necessary "tweakability" to optimize performance. In the case of RDMA, (2) may still be necessary in order to ensure program correctness and/or determinism (i.e., making sure all nodes compute a local sum before another machine collects the local results).

### 3: Something

### 4: Something

### 5: Dark Side

## Limitations and Extensions

## Editorial
