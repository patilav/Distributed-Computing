### Summary

This paper discusses the fundamental differences between distributed and local computing.
The authors argue that these differences make the paradigm of "unified objects" impossible to extend to a distributed computing setting.

### Outline

#### Intro

#### The Vision of Unified Objects

The current goal (at the time of writing) of distributed object-oriented computing is to represent all objects as equal from the perspective of the programmer.
The location of an object is considered to be an element of the implementation of that object; the location has no effect on the design.
As such, objects of this type may be solely defined by 1) a set of interfaces and 2) the syntax and semantics of operations on the interfaces.
Under the hood, the communication between objects that share an address space and between objects that don't varies, but the implementation of the communication is not considered part of the programming model and is hidden from the programmer.

Developing a distributed application occurs in three stages.
First, the application is design for correctness, regardless of location of objects.
Then, two stages of testing are used to determine locations of objects, tune performance, etc.

Most significantly, the author's description of the workflow of building a distributed application directly contradicts a point they make later. In the third stage of testing, they claim that "real bullets" (network/node failure) are used to determine a set of services (such as replication) that can be used to combat failures. Later, they claim that these partial failures doom the unified object model.

I claim that a mechanism could tolerate such failures.
So long as the system has deterministic rules for dealing with failure (which objects are "killed," which objects are responsible for re-instancing others, etc.), and objects can retain enough state of other objects to replicate them, the object model could be a reality for the application developer.
In this case, determinism can be used to combat non-determinism.

#### Deja Vu All Over Again

Here, the author discusses the development of the unified object model.
They claim the community is too focused on the low-level problem of communication paradigms and their implementations.

The author lays out the hard problems of distributed computing, namely partial failure, and lack of a central resource manager.

However, I wonder if the system could itself serve as a sort of *distributed* resource manager.
In other words, Could there be a fixed protocol built into the system for dealing with partial failure?
A counter argument I forsee to this possibilty is that such a protocol may require one object be responsible for the replication/restore of others, therefore acting as a central manager.
If only one node is responsible for the spawning of others, the application could reach a catastrophic state if the "head" dies.
My response to this is that like in a local computing setting, if the machine dies, so too does the application.

This point could lead to a discussion of a system where all distributed objects could be responsible for spawning others.

#### Local and Distributed Computing

This section is the "meat and potatoes" of the paper.
The author outlines three key differences between local and distributed computing, and the impact of these differences on the unified object model.

##### Latency

Latency, the author suggests, is the most obvious difference between local and distributed computing.
While tools could be used to determine communication patterns, and one could simply wait for hardware improvements to decrease latency, poor designs are still possible.
Fortunately, problems arising from high latency are problems of performance, not correctness.
Unfortunately, these problems may be impossible to solve if the objects are necessarily located in extremely remote locations.

However, if one is designing a distributed application it is likely the need for computations, memory, and storage make the cost of communication tolerable.
As well, latency does not break unified-object model.

##### Memory Access

Another key difference between local and distributed is the number of address spaces.
In order to achieve the unified-object model, it is necessary that the programmer not use address-relative pointers.
The author argues that changing the programming paradigm to limit such data access would be difficult.
They argue that either a facility needs to be implemented for translating local and remote memory addresses, or the programmer will need to be aware of the separate address spaces.

Both of the solutions retain the use of pointers, and ignore the underlying system. Consider this code:
``` int a = B.data[3]```
Upon compilation, it is trivial to believe that the system could 1) recognize B will be located remotely, and 2) transform the access to array `data` at index 3 into a network request.

##### Partial Failure and Concurrency

The key point discussed in this section is the fact that unlike in local computation, there is no central manager that can be queried to determine the cause and effect of a failure.

The author presents two extremes for solving the issue of partial falure:
* Treat all objects as local; partial failure is catastrophic
* Treat all objects as remote; system overhead is catastrophic

I believe that these solutions are too extreme, and there is a middle ground (unrelated to the final section).
I believe that it could be the responsibility of the system, at compilation time, to determine the location of objects and the proper interfaces to use for local and remote objects. For example, if an object is local, reference the object directly. If not, compile the interface for communicating with the object over the network (or on the same machine, different address space IPC-style).

The arguments surrounding concurrency are roughly the same.
The author argues either all object must ignore concurrency, and suffer in the case of failure, or pay the price of concurrency semantics.

Again, I argue a third approach.
In a local, single-threaded OOP application, objects act in a client-server style fashion. Why could this model not be extended to the distributed setting?
In other words, programming is synchronous, and function calls are semantically identical to server requests.
If requests to different objects (servers) need to be made simultaneously, allow for it but allow for synchronization afterward (e.g. a call to several compute nodes to do batch processing).

#### The Myth of Quality of Service

This section discusses the limitations of believing that any problems cause by latancy, memory access, concurrency, and reliability are all the sole responsibility of the implementation; the design should not worry about such issues.

The author then provides several anecdotes illustrating the problem.

I must take the stance, again, that while these *do* need to be problems dealt with, they do not necessarily have to be design decisions that come from the programmer.
The unified-object model can be maintained while the system can schedule, locate, implement interfaces, and do whatever else necessary to reinforce the correctness of the application with robustness in the face of partial failure.

#### Lessons From NFS

In this section, the author discusses some problems in early NFS that came from the application being distributed.
The key point they make is that NFS has problems that come from the fact that it implements a local computing model in a distributed fashion.
The local computing model used by NFS is typical file access calls (open, read, write, etc.).

The author argues that to fix this, one would need to change the interface.
I would argue against this by claiming that instead of providing a different interface, NFS could implement redundancy.
In this way, if a call to `read()` occurs and NFS determines the main server to be down (link or node), the operation can still be passed to the backup server. 
It may even be possible to learn from the backup server (which is in communication with the main server) how the main server died. Did it die mid-transfer and files were corrupted? Is it still alive and is the link the component that failed? Is it dead but all the data was copy pre-death? These could all be answered by a clever protocol (unless of course, connections to both nodes are severed... then we're doomed).

#### Taking the Differences Seriously

This section outlines how to proceed when creating systems for designing distributed applications.
Here, the author and I almost agree.
They argue that to overcome the differences in local and parallel computing, developers must designate the locations of objects in the design of the application.
Once the location of an object is defined, the compiler can take care of assigning the correct interface implementations.
They go on to argue that by requiring the developers to know the structure of the distributed application, it will be easier for them to accomodate for failures and the other problems that come from distributed computing.

I will again ask the question: Why can't the compiler determine locations and interfaces based on the design?

#### A Middle Ground

The author uses this section to define a third class of applications: local-remote.
These applications are multi-process but reside on the same node.
These applications are not completely distributed, because of the central resource manager and potential for shared memory regions, but are also not completely local because of separate address spaces.

### Limitations and Extensions

One section I thought was missing from the paper was a discussion of shared-memory, multi-threaded programs in the context of the unified-object model.
Do objects that run on separate threads share the same non-determinism as do concurrent processes running on the same or remote nodes?
Does the programmer need to specify shared-address concurrency and locks in the model, or is multi-threading simply not implemented by the "systems" referenced in the paper?

### Editorial

Overall, I thought the paper provided a clean, accessible description of the three key differences between local and distributed computing. 

I think there are two questions in the paper that need to be segregated.
The first is: Is the unified-object model possible?
The second is: Does the application developer need to recognize distribution?
In the paper, the author argues that the unified-object model is impossible because ultimately the developer needs to recognize the difference between local and remote objects in the application design.
However, I believe that the success of a unified-object model is independent of the required knowledge of the developer.
