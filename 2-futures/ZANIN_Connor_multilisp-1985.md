# Multilisp: A Language for Concurrent Symbolic Computation

## Summary

Multilisp is a language based on Scheme, but includes extra primitives for expressing parallelism. This paper covers the design decisions behind Multilisp, an implementation on the Concert CPU, and a short evaluation of the performance of Multilisp.

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

### 3: Multilisp

Multilisp shares 3 characteristics with Scheme

1. free variable references are resolved in the environment of the referencing procedure, not the one of the calling procedure
2. data passed to procedures is allocated on the heap and is abstracted by the enclosing procedure
3. recursive calls are optimized (procedure calls are reduced to jump instructions)

#### The Multilisp Approach to Parallelism

To mitigate the non-determinacy introduced by explicity parallelism *and* side effects, Multilisp is designed such that most code is written without side effects. When side effects are necessary, the language provides data abstractions so that calls to such data may be protected and synchronized.

I agree with this approach. Arguably the hardest part of designing a parallel application is thinking about how data should be treated. Putting the restriction of *mostly* side effect free code on the programmer might reduce the burden of parallelizing a serial algorithm. However, I do wonder if there are any algorithms that must heavily rely on side effects, and the possibility of implementing such algorithms in Multilisp.

Two constructs, `pcall` and `futures` provide the means in Multilisp for parallelism. `pcall` is used to evaluate the arguments to a procedure in parallel, and then call the procedure. This is analogous to a fork()-join() approach. `futures` allow memory allocation to take place concurrently alongside the evaluation of the value which will reside in such memory.

The authors talk about resourse allocation. Multilisp handles the scheduling of resources, but the amount of resources being used at any time is dependent on the amount of parallelism expressed in the program. Worryingly, the authors state that while the currently implementation of Multilisp performs adequetetly, they predict that as CPU speed increases they will need to reveal the power of resource allocation to the programmer.

### 4: Implementation of Multilisp

Here the authors give details about the hardware Multilisp is implemented on top of. They give details about MCODE, a "machine-level language" that is "interpreted by a program written in ... C." They go on to describe resource allocation, including the heap and garbage collection, and providing a limited performance evaluation.

The authors state that Multilisp runs as fast serially as another dialect of Lisp. Unfortunately, they give no formal performance comparison.

The performance evaluation is limited. They show that for an already embarassingly parallel algorithm, "nearly" strong scaling can be achieved.

Overall, Multilisp seems tightly coupled to the hardware despite earlier claims of being hardware-agnostic. In section 4.1, the description given of MCODE hints that there is more initial setup needed that the authors would like to reveal. They state "one copy of this... program is located in the local memory of... each processor." Additionally, they state the heap is shared among all copies of the interpreter. This bootstrapping is not described in detail, and is not clear about whether or not the copies of the interpreter are assigned per processor manually or by some external scheduler (an OS).

Given that this is an initial implementation, Multilisp can be afforded the cost of some manual setup. However, the authors contradict themselves by boasting the portability of their language while simultaneously hand-crafting the interpreter to the processor.

## Limitations and Extensions

Despite claims of portability, Multilisp was only shown to be implemented in one architecture. If the authors had ported Multilisp to another architecture, their case for the new language would have been much stronger.

Further, the evaluation of the performance of Multilisp is limited to one algorithm. It would have been nice to see a broader evaluation of the language, but at the same time it may be argued that distributed computing algorithms are outside the scope of the paper.

## Editorial

While I thought the implementation and evaluation sections were lacking, I did like the background information and introduction provided by the authors. The introduction layed a good foundation for understanding the challenges and considerations present in parallel language design, and provided a base knowledge. From there, they provided good motivation for the design desisions behind Multilisp.
