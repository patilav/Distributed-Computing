<meta charset=utf8>

# "Promises: Linguistic Support for Efficient Asynchronous Procedure Calls in Distributed Systems", Liskov, Shrira, Jun 1988

Avanti Patil <patil.av@husky.neu.edu>

## Synopsis
This paper deals with the concept of `Promises`: a new data type designed to support asynchronous remote procedure call mechanism to be used by components of a distributed program. It briefly describes `call-stream`: language-independent communication mechanism and examples of streams integrated in Argus programming language. The paper further goes on to describe how promises can be used with forks of local processes and compare mechanism presented in paper to related ones in other languages. It ends with brief on stream composition and evaluation of presented mechanism.

## Summary

This paper describes `Promise` as a data type which was developed after getting motivated by a communication mechanism called `call-stream`.  `Call-streams` combine advantages of (1) Remote procedure calls and (2) message passing.  Using `call-stream` mechanism programs can use components over a network in a heterogeneous computing environment by reducing latency. `Call-stream` vs `RPC`: (1) Call-streams allows the caller to run in parallel with the sending and processing of the call (2) Call-stream reduce the cost of transmitting the call and reply messages by buffering technique unlike RPC which send messages immediately. 

In `call-stream` a receiving entity provides one or more named ports; which are strongly typed and identify procedures that can be called from other entities. Exception handling is used as a termination model to avoid abnormal termination. Arguments and results are passed by value. Groups of ports which belong to same entity define the receiving ends of streams which are sequenced. Deadlocks are handled by no concurrent activity within an entity i.e. separate activists should not share the same stream. Agents define the sending ends of streams. An agent and a port group together define a stream. If the system is unable to live up to the guarantees, it breaks the stream. Since streams are based on buffering two important primitives provided to sender are flush and synch for all calls.

In next subsections paper describes the Argus `promise` mechanism and shows how promises integrate streams and local forked procedures.  `Promises`:  When a stream call is performed, the caller receives a `promise` for a result that will arrive later. A `promise` is an object that can be used to `claim` the result when it is ready which contains typed result or exception listed by handler.   A `promise` object is in one of two states: (1) blocked: as a part of making a stream call or (2) ready- when the call completes, the promise switches to the ready state and remains in same state. The `claim` operation waits until the promise is ready and returns normally else signals the appropriate exception. 

The paper follows series of alternatives to overcome different problems and reasoning for the same. Some of them are mentioned below

`Local forks` : Promises use deferred result for allowing concurrency between caller and callee and ordered processing of calls, but concurrency can also be maintained without the ordering. Local fork can be used in construction and access of recursive data structures such as lists and trees. `Composing Streams`: As a part of composing Streams, results should return to the original caller who then sends them on to the next stream. This kind of program form allows arbitrary filter computations to be done to “match” the two streams.

`Forks`: Forks are a general concurrency mechanism, so they can be used to implement the concurrent loops program structure. But the termination problem will arise in every stream composition; in each case, we will need the ability to treat the processes as a group and to allow a process in the group to cause the termination of the other processes in the group. Furthermore, programming proper termination explicitly can be quite tricky. 

`Coenter`: This is used to solve the termination problem by identifying the set of processes that implement a stream composition, so that they can be terminated properly when problems arise.  Argus provides a mechanism called `coenter` statement which provides complete and sensible treatment of exceptions and early termination. A `coenter` statement contains a number of arms, each defining a computation to be run as a process. A sub process can cause other sub processes to terminate early. It does this by causing a control transfer outside of the coenter. In this case, any remaining sub processes that are not yet finished are forced to terminate (as discussed further below) before the “parent” process can continue. 

`Filters`:  It would be more efficient to send the filters along with the calls.  However, such a structure is not practical in a heterogeneous system. since different programming languages may be in use at the ends of the streams. 

Conclusion: 
After carefully evaluating all the possible solutions this paper concludes that Promises support efficient asynchronous remote calls. Concurrency can be ensured by forks in a local and remote environment. Combining power of `forks` and `coenter` will allow compositions of stream while providing better termination handling. 

## Analysis

This paper was published in 1988 by the Advanced Research Projects Agency of the Department of Defense.  This shows that distributed computing had undergone quite a few changes from remote procedure call and message passing since the first conference Symposium on Principles of Distributed Computing (PODC) took place in 1982 [1]. As distributed computing continues evolving several problems surfaced like reduced performance in remote procedure calls due to waiting in caller. Call-streams were invented to take advantage of remote procedure calls and message passing.  Design of Promises is based on the concept called futures which was introduced in Multilisp in 1985. Futures in Multilisp were used to create tasks and synchronization of tasks.  `Promise` data type was developed after getting motivated by a communication mechanism called `call-stream` and extended `futures` introduced in Multilisp to be used in a heterogeneous computing environment.

Main contribution in this paper has been the provision of a mechanism `Promise` that preserves the merits of organizing programs using procedures and procedure calls without sacrificing the performance benefits of call-streams. 

Some attributes of Promises are 
(a)Strongly typed (b) allow the result of a call to be picked up later (c) allow exceptions from the called procedure to be propagated in a convenient manner (d) handle node failures and network partitions using call-stream mechanism.

An example of calculating and printing results of students is displayed and justified in the paper using Argus programming language demonstrating the call-stream communication mechanism. This examples describes various scenarios for call creation with custom encoding, result generation, exception handing and toggling states of promise in ready/blocked state.
This example uses stream calls both to overlap processing of calls and to obtain the benefits of buffering messages for calls and replies. 

Authors talked about problems with RPC after carefully analyzing the delay introduced in processing each call. Buffering messages will save lots of time for call-streams, especially for small calls and replies and improve Latency. This mechanism was introduced with clearly finding out problems due to buffering and solutions to them. 

Further discussion reinstates the importance of Multilisp futures for inspiration of Promises. Authors talk about disadvantages of futures (1) inefficient to implement unless specialized hardware is available due to type safety and (2) poor error handling - difficult for a program to determine the reason for the error value. 

Authors discussed that Coenter offers a complete and sensible treatment of exceptions and early termination.
Advantages of coenter were remarkable: (1) For stream composition coenter allows us to indicate directly what processes are involved in the composition, which in turns allows those processes to be terminated as a group if a problem arises.  (2) The code of the processes can be written in line while using coenter. 

Limitation of `Coenter` is that it is appropriate to use `coenter` mechanism when the processes have no results and when the control structure is naturally hierarchical. When these conditions are not satisfied, a fork will be a better mechanism. 

Authors continue to mention the Important aspect of synchronization would be needed to ensure that the calls on each stream were made in order. To implement such a structure, there should a way of spawning a dynamically determined number of processes. Although the concurrency can be obtained by forks, this leads to the same group termination problem. Instead a mechanism with automatic grouping is preferable. Argus provides such a mechanism, which extends the coenter to allow a dynamic number of processes.  

Paper mentions, instead of using coenter or forks, another possibility is to provide a construct that supports composition directly. Such a structure could lead both to simpler programs and better performance. However, it is not clear that stream composition is important enough to justify its own linguistic mechanism. At present, we believe that the coenter form is adequate for our needs. 

At the time when paper was written Using promises for asynchronous remote Calls was entirely new. However, many languages had local concurrency of the “fork” variety. This shows the importance for the 

Comparison to other languages by Authors:

(1) This paper claims that Argus is type-safe and handles exceptions but Mesa and Modula-2+ miss out on exception propagation to the outer block.  

(2) Programs in Ada and SR languages can be optimized only to reduce the delay of individual calls, not to improve the throughput of groups of calls. 

(3) The send/receive approach used in Plits and MOD can allow programs to achieve high throughput, but it leads to complex and ill-structured programs. 

Authors also considered the use of promises with forks. Promises for streams have three properties: (1) concurrency of caller and callee, (2) caller control of claiming and putting promises in data structures, and (3) ordering of the processing of a sequence of calls on a stream. Promises for forks have only the first two properties, but are nevertheless very useful. In particular, the ability to propagate exceptions from the forked process to some other process in a convenient manner is extremely useful, and represents a solution to a problem that has been a concern to language designers in this area.

Paper concludes that promises are a good way of supporting efficient, asynchronous remote procedure calls in a programming language by carefully explaining and evaluating further concepts like `forks` and `coenter`.

## References
[1] ACM Symposium on Principles of Distributed Computing: http://www.podc.org/history/

[2] Halstead, R. “Multilisp: A language for concurrent symbolic computation”. ACM Trans. on Programming Languages and Systems 7, 4 (October 1985). 
