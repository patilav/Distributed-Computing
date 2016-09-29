# Orleans: Cloud Computing for Everyone

## Summary

This paper introduces Orleans, which is a programming model and runtime for easily developing scalable, elastic,fault-tolerant, and consistent cloud applications. Its primitive execution unit is based on the Actor model, and programs are written in an asynchronous style, combining promises and callbacks.

## Analysis

### Introduction

Orleans is a simultaneously a programming model and a runtime designed for writing scalable, elastic, fault-tolerant applications; this framework is targeted at a cloud environment. The main consideration when designing Orleans was the ease-of-use of the model by the programmer. The authors wanted to make it possible for non-expert developers to write applications that could take advantage of the resourses in the cloud, while abstracting the problems of scalability, partial failure, and consistency from the developer. These problems are handled by the runtime.

At the base level, Orleans relies on Actors, which they call "Grains." Grains adhere to a single address space, single threaded model. There can be multiple instances of a Grain (called "activiations"), but the single-threaded, single-address-space model allows these instances to be spread across multiple nodes.

Grains communicate through RPC-style calls. These calls return promises, which can either be applied to other computations or waited on explicitly. The paper states that a Grain handles these asynchronous requests one-at-a-time; presumably the runtime queues requests made to Grains.

On top of Grains, Orleans has a notion of a "Transaction." A Transaction is a request to the application, and each Transaction involves a known set of Grains. The activations of the Grains that will handle the request are isolated from the activations of Grains handling different requests. If any of the activations fail, Orleans will handle cleanup and re-execution of the request.

After reading the introduction, I am most curious about the application model that the authors expect Orleans to accomodate. I am curious because they mention briefly that Grains have persistent state, and each Activation of a Grain can make changes to the persistent state (Grains also have their own local state). I am curious about how Grains will be applied and how they will deal with consistency in real applications.

### Programming Model

Promises are used to coordinate asynchronous computation. In Orleans, a promise can be obtained in two ways:

1. An RPC-style method call to another Grain
2. A call to a Delegate

Delegates are methods that are called when a promise becomes *resolved* (either success or error).

This section also discusses the implementation for creating Grains, Grain factories, and defining Grain interfaces. A point to note here is the exclusion of .NET property setters from Orleans (they return void). However these can easily be replaced by method calls to a grain.

Another thing they talk about is the execution model for Grains. Earlier, they mentioned that Grains process requests one at a time. They note that while this is true of Orleans (each Grain is single-threaded), there is non-determinism present due to the asynchronous nature of promises and the callbacks (Delegates) that process completed promises. To address this, they warn the programmer to be aware of state and to take care when Grains operate on a shared piece of state.

### Orleans Runtime

The Orleans runtime must cope with with the challenges of computing in a cloud environment and abstract some of those challenges from the programmer. These challenges include persistence, replication, isolation, resource management, reconciliation. These problems can be grouped into three major categories: state reconciliation, load balancing, and partial failure.

To address shared state reconciliation, Orleans provides a default mechanism as well as an API for defining specific reconciliation algorithms. This is powerful, because the programmer can control how changes to a Grain's state made by different Activations get resolved. For example, if two Activations change a users subscriber count, an algorithm could be defined to atomically compute the net change.

The authors are vague about the load balancing performed by the Orleans runtime. They say that requests to a Grain are "randomly distributed to existing activiations." They say that if an Activation has a full queue, a new Activation will be created on the same node. They do not mention what happens in the case where all nodes containing Activations of a Grain are busy. Presumably, a new node is requested and an initial Activation is "spun up."

A major problem they mention is Orleans' data store. In section 3.8, they discuss the considerations that Orleans needs to make when assigning Activations to Transactions. Namely, the two key considerations are Atomicity and Consistency. A solution I might offer to this problem of "Activation scheduling" is to run the Transaction->Activation assignment->Reconciliation cycle in batches. In this way, presicely one Activation can be assigned/created for each Transaction and the data structures needed to keep track of where state changes have been made can be simpler. If creating Transaction batches introduces too much overhead, an alternative could be to simply create a new activation for each transaction, and run the reconciliation in the background (in batches).

## Limitations and Extensions

The contributions of this paper are not great when considering computationally intensive applications. For "HPC" applications, Orleans merely offers a mechanism to recover from partial failure. Since this mechanism is implemented at the Transaction level, it is identical to a HPC "job" failing. Re-running is the solution in either case. 

The contributions of this paper are major, however, when considering applications that are logically simple and have persistent state, but have unpredictable workloads. In other words, user-facing applications like social networks are perfect candidates to benefit from the ease-of-development that Orleans offers. The authors of Orleans have built the system around the Transaction (request) model, which mimics the client-server model of web applications.

As stated in the paper, a place for extension is the reconciliation system. Now, they have defined a loose way of assigning Activations to tasks. They could expand on their work by creating a more concise algorithm for Transaction creation, assigning Activations to Transactions (or creating them), and reconciling the modifications to Grains made by Transactions.

## Editorial

Overall, I liked the paper. I didn't like the structure, which spread topics accross multiple sections (mainly introduction and either model/runtime sections). The introduction (which they seemed to have crammed into the abstract) served less as an introduction to the current state of the practice or the problem, and more as an introduction to the system. However, complaints about structure are minor and I did think they did a good job of giving both a high level overview of the system as well as a low enough level one; I was able to have a mental picture of the implementation without getting bogged down in details.
