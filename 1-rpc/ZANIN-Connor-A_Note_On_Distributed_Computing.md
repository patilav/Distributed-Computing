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
In order to solve the latency problem, changes to the distributed programming paradigm need to be made.
Thus, the vision outlined earlier seems infeasible when faced with the the most apparent difference between the local and distributed models.

However, if one is designing a distributed application it is likely the need for computations, memory, and storage make the cost of communication tolerable.

##### Memory Access

##### Partial Failure and Concurrency

#### The Myth of Quality of Service

#### Lessons From NFS

#### Taking the Differences Seriously

#### A Middle Ground

### Limitations and Extensions

One section I thought was missing from the paper was a discussion of shared-memory, multi-threaded programs in the context of the unified-object model.
Do objects that run on separate threads share the same non-determinism as do concurrent processes running on the same or remote nodes?
Does the programmer need to specify shared-address concurrency and locks in the model, or is multi-threading simply not implemented by the "systems" referenced in the paper?

### Editorial

Overall, I thought the paper provided a clean, accessible description of the three key differences between local and distributed computing. 

I think there are two points 
