This paper gives an overview of the Finagle system, which is an asynchronous RPC system built for the JVM. In a large scale setting, servers need to handle a very large volume of requests asynchronously and be able to handle things like partial failures, network changes and other failures, while at the same time, need to work together with a huge set of hetrogenous components. All of this is at odds with the idea of modular and reusable softwares.

Finagle system is based on three abstractions which combine together to make a really good programming models for building modular, efficient and safe server.
They are :
1. Futures : Acting as a proxy for the result of some asynchronous operation.
2. Services : Asynchronous functions which represent a system boundry.
3. Filters: Encapsulate application-agnostic concerns like timeouts and authentication.

Operations on server are defined in a declarative manner, combining the results of many sub-operations by combining futures.Operations are termed as value transformations. They describe what is computed and the execution is handled seperately. This increases programmer productivity as a lot of tasks like memory management, creating threads etc are taken care of by Finagle. 

The system is currently in deployment at Twitter with a lot of success.

## Futures
A future is used to hold the results of an asynchronous operation, like a network operation or a disk I/O operation. It can either be empty if it the operation is not yet finished, or it has succeeded and the operation has finished successfully, or it failed and the future contains the exception.

Futures can be composed as either as a function of other futures, giving rise to a dependency graph or as independent futures which are executed concurrently. They are a first class value defined in the host language. They can be chained together. This combined operations are analogous to unix pipes and are called future transformation.

## Services and Filters
A service is a asynchronous function representing a remote endpoint. It is different from a regular function as by it's return type which is a future. Services can represent clients and servers symmetrically. Finagle provides clients with the instances for Service which can represent either concrete or virtual remote servers. They are used to represent the logical application components inside an application. 

Filters are used to represent the application-independent concerns like timeouts, recording statistics, authentication and retry policies. They are used with services to modify the service behavior. They are simply functions and provide a combinator, andThen which is used to chain the filters with either other filters or services. Filters can be applied both on client and server, and they can transform the requests and responses.



## Discussion
#### Declarative programming with futures
Futures forces the programmer to adopt a declarative approach towards structuring the system as a set of components. Data dependencies are handled by future combinators. This forms a system description, seperating the semantics of operations from execution. Programmers are freed from the tasks like managing threads and cleaning up after resources as the execution part is handled by Finagle system.

This also enables modularity as we separate concerns of semantics from execution. Finagle concentrates on efficient execution. 

Thinking about data-flow over control-flow means the code is well behaved under non-deteministic concurrent operations. The code flows as a transformation of immutable values instead of a sequence of operations on shared data. 

Futures are relatively lightweight. In the current implementation, it takes 16 bytes for the central data structure.

#### Futures in practice

Futures are read-only. Also, with futures, since consumers have no knowladge of producers, this can lead to resource leaking. If something like a time-out occurs, the underlaying operation is not terminated. This is a problem as it hogs resources if the network connections are limited. 

Finagle introduces an *interrupt* mechnism to take care of this problem. Interrupts flow in the opposite direction of futures, and enable consumers of a future to notify if the result is no longer needed. The interrupts are upto producers to be acted on. It enables the cancellation of an operation.

While interrupts tend to violate the pure data-flow model of the futures, consumers are still independent of the producers. They come with their own set of challenges. Should we propogate interrupts to all the futures, or only the outstanding ones. What about shared futures ? Still, interrupts work in practice and twitter hasn't faced any problems yet.

#### Filters

Filters are used for things like logging, request sanitization, traffic control, timeouts and so on. They help with modularity and reusability and testability of the code.

#### The cost of Abstraction

The high level language constructs come with their own problems, like garbage collection. New futures are allocated on the heap by future combinators, along with the closures. People developing Finagle created many tools for analyzing the allocations. 

Although in isolation this is only a small service degradation, the large scale of operations amplifies this problem. 

A moajor source of problem with garbage collection comes from unintended capturing of references in the closures. If the operations are long lived, this amplifies the problems, as sometimes closures can be tied down to the life of a connection and not the requests. Fine-grained control over the closure environment are one way to mitigte the issues with garbage collection. 

The people developing Finagle came up with a tool, JVMGCPROF which provides reports on allocation rates and lifetimes. there is an ongoing effort area.

#### Futures, Services and Filters at Twitter

 At twitter the techniques are especially useful in middleware servers with services as an interface. It depends on other services and acts as "future transformers". This system is expressed in a declarative fashion. Filters collect statistics, tracing and other runtime information without the operators knowing the specifics.


## Related work
LWT is a cooperative threading library in OCaml with a lightweight thread similar to futures.
Dataflow programming languages also enforce dependencies between computations, but require determinancy. In the raw form therefore, they are not suitable for sytems. There is a proposal to split these languages into deterministic parts connected by nondeterministic channels.

Haskell and Go come with cheap user-space threads, reducing the overhead for thread-based concurrent systems. The cost associated with managing these threads is cheap, again freeing up programmers from the extra work of managing these resources. But the threads don't provide the same clean data flow models as Futures and are part of the language model, so are not allowed to be managed/optimized at the runtime. 

