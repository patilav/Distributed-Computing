# Dataflow Concurrency in Oz: Analysis
Nathaniel Dempkowski

## Summary

Dataflow concurrency allows for operations to wait when they try to use variables that aren't bound yet. This means operation scan be done in any order with any kind of delay and execution and have the same result. Calculations work correctly independent of thread partitioning, as long as they are given the same arguments. This gives most of the advantages of concurrency without the usual complexities. Shared dataflow variables can be used as synchronous and asynchronous communication channels or synchronization points. Dataflow variables are useful for improving latency tolerance and third-party independence in distributed programming, and provide a nice middle ground between having no state and having state.

## Outline

What happens when a computation tries to use a variable that isn't bound yet? Aesthetically it would be nice if it waits for the binding and continues. This behavior is known as dataflow. This behavior means that operations can be done in any order with any kind of delay and the execution will always have the same result. This leads to a couple of nice properties: calculations work correctly independent of thread partitioning and calculations are patient, they wait instead of signaling errors. As long as operations are invoked with the same arguments this doesn't change the results. This itself is a key property of dataflow concurrency, which leads to it having most of the advantages of concurrency, without the usual complexities.

The declarative model is based around a single assignment store. Variables can be bound to one value, and once bound they are indistinguishable from value. A value store maps variables to values. Functional languages like ML and Haskell use an immutable data store as they compute on values. Object-oriented languages like C++ and Java need a mutable cell store. The single assignment store allows computation with partial values, like returning output by binding an unbound argument. This store enables declarative concurrency, which is necessary for relation/logic programming. It is also very efficient.

One important aspect of this store is partial values, meaning data structures that may contain unbound values. We can say a set of partial values is "compatible" if unbound variables in them can be bound as a way to make them all equal. Variables can be bound to variables, and you can have multiple variables in the store bound to a single value.

Different languages offer different possibilities when trying to use a variable before its bound. These listed in order of programmer niceness are:
* Execution continues with no error, with undefined garbage in memory. The programmer is not aware this happens and undefined behavior occurs. Eg. C++
* Execution continues with a default type-dependent value. The programmer is not aware this happens, but the program always gives the same result. Eg. Java fields
* Execution stops with an error or exception. This is reasonable in sequential programs, but unreasonable in concurrent programs as execution becomes nondeterministic depending on error timing. Eg. Prolog 
* Execution is not possible, as unbound variables are detected by the compiler. Eg. Java local variables
* Execution waits until variables are bound and then continues. Eg. Dataflow, Oz. This is unreasonable in a sequential system, as you could wait forever, but reasonable in a concurrent system. This also introduces a new type of error, waiting forever.

Declarative variables that cause a program to wait until they are bound are called dataflow variables. These are very useful in concurrent programming, and order independence makes declarative concurrency possible.

Dataflow variables can be used as communication channels. Threads can communicate through shared dataflow variables. You can consider a dataflow variable a communication channel and a thread as an object. A property of these channels is that only one message can be sent, but that a message can be received many times.  This enables both asynchronous and synchronous sends and receives. The mechanisms for these are as follows:
* Asynchronous send: bind a variable
* Synchronous send: wait until a variable is needed
* Asynchronous receive: use variable immediately
* Synchronous receive: wait until a variable is bound

It is also possible to implement non-blocking sends and receives. With the dataflow non-blocking sends are always successful. A non-blocking receive consists of checking if a variable is bound and returning true or false. This is implemented via a non-declarative operation.

Synchronization between threads is a fundamental property that emerges when one thread needs the result of a calculation done by another thread. We say that it synchronizes on the availability of the result. This is a fundamental concept of concurrency. If a thread depends on another's results, this creates another constraint on computation called a synchronization point.

Dataflow synchronization is implicit synchronization, present in the semantics on the dataflow model. It is not visible in text. The alternative is explicit synchronization which is visible in text, and is an explicitly programmed trigger like a lock, monitor, or mutex. These are primarily found in languages like Java and C++. Additionally, there is supply-driven synchronization (eager execution) which waits until arguments are available and synchronizes on the availability of arguments, and demand-driven synchronization (lazy execution) which attempts to compute the arguments when necessary, synchronizing on the operation needing the arguments. Out of the four synchronization possibilities that these traits enable, it was found that concurrent programming is simpler with implicit synchronization, and that dataflow execution makes concurrent programs simpler. This leads to the conclusion that dataflow variables are "spectacularly expressive" when compared to explicit synchronization, even without explicit state.

Dataflow variables are a powerful primitive for concurrent programming. Many techniques become simplified, and new techniques become possible. It removes order dependencies between parts of a program replacing static dependencies determined by the programmer with dynamic ones determined by the data. For distributed programming this improves latency tolerance and third-party independence. This is a middle ground between having state and having no state. Dataflow variables are stateful because they can change state, but they can only bind to one value. They are stateless because binding is monotonic meaning more information can be added to partials, but none can be changed or removed.

Futures and I-Structures (incomplete structures) are two additional implementations of dataflow execution. These are both single-assignment variables. These allow the programmer to manually increase the potential parallelism by removing inessential dependencies between calculations. Futures allow for concurrency between a computation that calculates a value and the computation that uses that value. They return a placeholder results and initiate a concurrent evaluation when created. When the value is needed, the program then blocks until it is available. The future can only be bound to by the concurrent computation that is created along with it. I-Structures are arrays of single-assignment variables where individual elements can be accessed before all elements are computed. This permits concurrency between a computation that calculates the array elements and one that uses their values. The fundamental difference between dataflow variables and futures/I-structures is that the latter can be bound to only once, where dataflow variables can be bound to more than once with consistent bindings. This is an advantage for efficiency in certain applications or data structure implementations.

## Limitations and Extensions

Limitations exist surround the types of programming dataflow variables are useful for. It was pointed out that with dataflow variables, execution can wait forever for a result to be computed, which is totally unreasonable in a sequential system, as that result will never be computer. This introduces a new type of error to the programmer. They generally have to deal with it in sequential languages in the contexts of infinite loops, but now that error is introduced silently into every variable, so rather than explicitly failing in a sequential system, programs now wait forever for variables that are never going to exist. Additionally, there are limitations around the guarantees that a system implementing dataflow concurrency is forced to deal with. When talking about synchronization they basically say that processes can't have an infinite number of steps and that this should be enforced by the system. This sounds nice, but in practice scheduling is a fairly difficult problem, with many subtleties around efficient scheduling. I think that the idea of making all dataflow into essentially an asynchronous graph sounds very promising, but that from a programmer perspective, you may unintentionally introduce data dependency cycles without a good mental model for reasoning about dataflow concurrency. Without using this system for more complex applications, it is hard to say if you run into lots of implementation-based subtleties around concurrency. The promise here is same result given same input, but when you bring networked data into the mix does that actually hold?

I'm not sure about extensions on top of this model. It seems like all of the programming principles that are used in other languages could be implemented on top of dataflow concurrency if desired. Schemes were offered for communication and synchronization that seemingly meet any programmer's needs. I am curious how difficult it would be to implement a dataflow programming model in an existing language. How leaky would the abstraction be?

## Opinion

As whole, the dataflow concurrency model Oz offers seems very compelling. It seems to offer some nice guarantees theoretically to the programmer around concurrency and deadlocking. I am curious if these guarantees hold in practice, and I think there are a lot of implementation details of the system that implements dataflow programming that you might have to get right for it to really work spectacularly. I think that having implicit concurrency everywhere is a really nice abstraction, but I am curious how leaky it is. There didn't seem to be many examples of systems built with this in practice, or challenges they faced, though this was written in the context of a textbook. Additionally, the different implementations of communication channels and synchronization that fall out of the dataflow programming model seemed quite thought-provoking and elegant.