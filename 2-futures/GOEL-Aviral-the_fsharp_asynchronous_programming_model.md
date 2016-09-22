# The F# Asynchronous Programming Model

Authored by *Don Syme*, *Tomas Petricek*, and *Dmitry Lomov*

Summarized by *Aviral Goel*

---

## 1. Summary

The authors describe an asynchronous programming model for the F# programming language along with its applications to reactive, parallel and concurrent programming. They achieve this by augmenting the core language with a non blocking modality in a relatively non intrusive way. The new construct, `async`, representing a computation to be run asynchronously, is translated by the compiler to a function accepting continuations which is then executed on a thread pool or on a single thread. The implementation allows asynchronous loops, exception handling, resource compensation and cancellation. The new construct is compositional, functional and scalable; easing multicore, I/O and actor based programming. This enables programmers to replace the direct use of OS threads and to nicely write event-based code for better scalability of web and multi-core applications.

## 2. Details

### 2.1. Overview and Implementation

To enable asynchronous programming, the language has been extended with a new type `Async<T>`. This represents an asynchronous computation which upon execution produces a value of type `T` and delivers it to a continuation. Since callback based asynchronous code is highly non linear and leads to inversion of control, the authors have added another construct, `async`, to the language. Within this construct, control flow expressions have an asynchronous interpretation. The compiler does a CPS translation of the `async` expression to a function accepting continuations for success, exceptions and cancellation, along with a cancellation token. The function eventually calls one of these continuations. The `async` expression is thus reduced to core language expressions. The only asynchronous action is `asyncsleep` which raises a wake-up event after an arbitrary period of time. Except when performing CPU computations, asyncs don't block at all. The semantics of the implementation ensure that with one thread the system becomes a deterministic queue of event reactions, each run to completion or to an `asyncsleep`; and with multiple threads, the system degenerates to a thread pool, running reactions to events in parallel.

### 2.2. Features
`async` expressions can be wrapped around function body resulting in asynchronous functions. They can also be used to express asynchronous loops.
The implementation extends the language to support exceptions in `async` computations in a natural way. Support is also provided to deterministically dispose off resources such as file handles and database locks at the end of a lexical scope, regardless of success, failure or cancellation. Implicit cancellation mechanism is also baked into the design. Cancellation checks are made at each I/O primitive, before each binding and exception construct and before each iteration of asynchronous loop. Thus, cancellation is not immediately effective as it cannot occur during core language code and it cannot skip resource reclamation. This avoids the subtle behaviour exhibited by pre-emptive cancellation of code with side effects.

### 2.3. Concurrency Patterns
The `async` expression forms a monad and can bind a result from another async computation. This allows for parallel composition of computations to achieve fork join parallelism and promise-based parallelism. Reactive agents can also be created which encapsulate a message queue and asynchronously react to messages received from other such agents, allowing one to model actor based concurrency. Furthermore, cooperative resumption based asynchronous tasks are a perfect fit for Graphical User Interface design. 

## 3. Performance and Usability
The authors conducted a case study in which they recorded the program length and programming time for implementing a TCP server using `OS Threads` and `async` in both `C#` and `F#`. They found that the `async` version was much faster to code in `F#` and overall scaled far better than the thread based version for both languages.  They also compared the `async` actors of `F#`, `Scala` and `Erlang` and concluded that 'for all these languages, the in-memory processing costs are nearly always "good enough" for read-world asynchronous programming'.

## 4. Merits
The paper is well structured and self contained. Motivating the need for asynchronous programming, the authors describe how their implementation builds upon the language without disturbing compilation via .NET and interoperability with .NET libraries. The compositional nature of their implementation fits well with the strict functional nature of the language. Attention has also been paid to exception handling, deterministic resource disposal and task cancellation within asynchronous computations. The formal model provided is fairly neutral w.r.t. the core language and yet succeeds in properly describing the semantics. Furthermore, the authors also describe a few design patterns on top of their implementation. They finally conclude with a pragmatic case study.

## 5. Further thoughts
1. The authors claim without references that 'light-weight threads are difficult to integrate in a language/VM without affecting performance of CPU-intensive native code'. The question - Why are kernel level threads slow and how can we address this problem with efficient user level threads supported by the compiler? still seems open. This [Google talk](https://www.youtube.com/watch?v=KXuZi9aeGTw) delves into the issues with thread implementations and proposes a modified API. Similarly, [this paper](http://dl.acm.org/citation.cfm?id=1251058) talks about improving thread performance. 

2. Even if we have good thread implementations, how will we compose them? One would like to manipulate parallel computations as building blocks to create bigger building blocks. The authors hit the nail on the head when they say - 'Events v. threads is a major topic in systems research, with papers highlighting the duality of the two approaches, and less on expressivity.'. I believe that even if threads are made faster somehow, we still need useful abstractions for composing parallel computations and we will end up developing the same abstractions as we have for event based model. 

3. Through CPS translation, an `async` expression is transformed by the compiler to core language constructs. This is just syntax rewriting which can be easily done with macros. Though implementing a macro system would definitely be orthogonal to implementing an asynchronous computation model, but it would have probably allowed `async` to be written as a library component rather than a core compiler construct. Why view a language as a fixed set of rules?
