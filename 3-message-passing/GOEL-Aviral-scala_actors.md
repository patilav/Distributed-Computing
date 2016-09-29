# "Scala Actors: Unifying thread-based and event-based programming", Philipp Haller, Martin Odersky

Summarized by *Aviral Goel*

---

## Synopsis
The paper discusses the design of a message passing concurrency construct `actor` for Scala. This work unifies both event based and thread based message passing. The design builds upon the elegant abstraction facilities of Scala, allowing the authors to implement actors on top of unmodified JVM. This work is novel, for it is apparently the first successful attempt at incorporating Erlang style message passing using lightweight isolated processes assuming only the basic threading model of the underlying runtime. The design simplifies many concurrency related problems. Its performance and scalability are impressive.

## Summary
The authors address message passing concurrency because of its potential to simplify challenges of multicore programming, distributed systems and web services. They view the Actor model, implemented and popularized by Erlang, as an attractive option, as yet missing for the JVM platform. Existing concurrency constructs such as shared-memory threads with locks suffer from high memory consumption and context-switching overhead whereas explicitly event driven style is complicated due to inversion of control. 

An actor is a concurrent process that communicates asynchronously with other actors by exchanging messages. Actors can respond to messages by creating new actor, sending messages to other actors, or changing their behavior. The body of an actor is a `PartialFunction` which consists of a set of message patterns and the value to be returned if the received message matches the pattern. Incoming messages are queued in the actor's mailbox. Messages are scanned in their order of arrival and the first message which matches a pattern is removed and processed. Mailbox access is race free by design. 

The authors have unified thread and event based actors in a library which requires no special compiler or runtime support. Thread based actors support blocking operations such as I/O and can be executed in parallel on multiple cores. They wait for messages using the `receive` operation. Event based actors use the `react` operation. They are lightweight and a lot of them can be active simultaneously, and they are fully interoperable with VM threads. 

When a new actor is spawned task is generated to execute it. When an actor is suspended or is not defined for a message, then the message is enqueued in its mailbox. Otherwise, its internal state is changed to reflect that it is no longer waiting for a message. If the actor is suspended on a `react` then a task is scheduled to process the message. The task wraps an invocation of the continuation of the actor in an exception handler. Once the message is processed, the actor detaches by storing its continuation closure and an exception is thrown. This allows the control to be transferred to the point in the control flow where the current actor was started or resumed. If the actor is blocked in a `receive`, it is resumed by notifying its thread which is blocked on a call to wait. `receive` retains the complete call stack of the receiving actor, hence such an actor can be resumed without invoking any continuation.

Actors are always executed as part of a task. They are submitted to the thread pool for execution by the scheduler. Multiple tasks are executed on multiple threads for parallelism and and to prevent blocking operations from affecting progress of other tasks. The size of thread pool remains fixed as long as event based `react` method is used for message passing. If some actors use `receive` operation, blocking every worker thread, pending tasks which could possibly unblock any of these worker threads are left unprocessed indefinitely. To prevent this deadlock, a scheduler thread periodically checks for this condition and adds a new worker thread to the thread pool to service the pending tasks. Each worker thread has its own local task queue. Though the task for a receiving actor is scheduled for execution on the same thread as the sender, it may be executed by another worker thread as a consequence of work stealing. 

## Extensions
The authors extend the implementation by providing combinators such as `andThen`, `loop` and `loopWhile`. These allow event driven actor behavior to be composed sequentially without sacrificing modularity. Internally, these combinators attach hook functions which get executed whenever the the actor terminates execution. The authors further extend their "dynamically typed embedded domain-specific language" by providing `Channels` which can be parameterized with the types of messages that can be sent and received from them. They permit strongly-typed and secure inter-actor communication.

## Examples
The authors take the example of a pair of processes that exchange data over a FIFO pipe. They provide fragments of code that solve the problem using threads, events and actors. Thread based version, due its linear structure is easier to read as compared to event based version. The actor version improves further by having fewer global variables and simpler synchronization. But the event based version scales better. The event based actor version combines all the benefits. This lends further credibility to the main goal of this paper, to provide actor abstraction in both event based and thread based fashion. 

## Benchmarks
In the end the authors conclude with some benchmarks. 
- Message passing throughput of actors compares favorably with Java threads. For relatively few processes, Java threads outperform actors by a small margin. While the thread performance caves in as the number of processes increases, the actor model maintains a nearly constant throughput.
- The actor models' throughput scales linearly with the number of processors.
- Actors enable a significant speed-up in their network scalability benchmark in which they simulate the effects of a large number of mostly-idle client connections.

## Key Contributions
Assuming only the basic thread model of the underlying host, the authors have successfully managed to implement Erlang's programming model into an object-oriented and functional programming language in both event based and thread based forms. They have also enriched their model by adding new forms of composition and channels for inter actor communication. This also speaks in favor of Scala's good abstraction facilities. The benchmarks and examples lean favorably towards their unified approach. A further testament to the success of this work is the popularity of Scala's actor model.

## Further Thoughts
The authors have not discussed performance issues arising due to very large messages. Since processes are isolated, messages have to be marshalled and unmarshalled. This will be costly for big data structures.
