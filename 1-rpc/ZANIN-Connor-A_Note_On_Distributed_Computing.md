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

Developing a distributed application by this model occurs in three stages.
First, the objects and interfaces are defined.
Next, testing of the application is performed to learn the communication patterns between objects.
In this stage, tuning can be done; the placement of objects can be tweaked based on the communication patterns.
Finally, the application is tested on real machines and real networks.

A key argument behind this vision is that the implementation has no impact on the correctness of the program.
While theoretically true, the author discusses later why this vision is infeasible in practice.

#### Deja Vu All Over Again

There exists a cycle of communication paradigm and implementation development.
Unfortunately, problems in local computing are different from those in distributed computing.
As programming paradigms evolve, so too must the communication paradigms; thus a cycle of programming and communication development occurs.

The author argues that the community's focus on paradigms and implementations of communication is incorrect.
This work does nothing to solve the *hard* problems of distributed computing: partial failure, lack of resource manager, and concurrency.

#### Local and Distributed Computing

There are three fundamental differences between local and distributed computing as defined in the paper.

##### Latency

Latency, the author suggests, is the most obvious difference between local and distributed computing.
While tools could be used to determine communication patterns, and one could simply wait for hardware improvements to decrease latency, poor designs are still possible.
In order to solve the latency problem, changes to the distributed programming paradigm need to be made.
Thus, the vision outlined earlier seems infeasible when faced with the the most apparent difference between the local and distributed models.

##### Memory Access

##### Partial Failure and Concurrency

#### The Myth of Quality of Service

#### Lessons From NFS

#### Taking the Differences Seriously

#### A Middle Ground

### Limitations and Extensions

### Editorial
