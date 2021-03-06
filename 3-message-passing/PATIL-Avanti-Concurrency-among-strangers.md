# "Concurrency Among Strangers - Programming in E as Plan Coordination", Miller, Tribble, and Shapiro
Avanti Patil <patil.av@husky.neu.edu>

## Synopsis

This paper presents plan coordination challenges in distributed environment introduced by  (1)concurrency, (2)large latency and partial failure and (3)misbheviour of machines. E programming language addresses these challenges, by changing few concepts of conventional sequential object programming. This paper focuses on E’s support for concurrent and distributed programming within the constraints of limited trust.

## Summary

Main contribution:

(1) E works by dividing the state of a computational system into separately encapsulated objects
(2) E gives objects very limited access to each other
(3) E limits outside interference and extend the range of assumptions for programs to safely rely upon.
(4) E uses abstraction as a way to limit one object’s need for knowledge of others which reducing the number of cases which are relevant for case analysis.
`Limitation`: However, even under sequential and benign conditions, the remaining case analysis can still be quite painful.

Authors provide an example of `The squential "StatusHolder"` for coordinating changing status between publishers and subscribers. This pattern can be used to coordinate a lot of loosely coupled plans, but this still creates plan interface hazards like:
(1) Aborting the wrong plan - Imapct of failure of single subscribers resulting in multiple failurers
(2) Nested Subscription - additional subscriptions generated due to other activities and their impact on current subscribers
(3) Nested Publication - resulting in muliple override of data

`A Taste of E`: 

(1) E has no classes. 
(2) E creates a new object with the enclosed method definitions and bindes the new variable to this object. 
(3) Invocation of a method causes a message to be delivered to an object. 
(4) When object receives message it reacts according to the code of it's mathching method. 
Basically all values are objects and all computation proceeds only by delivering messages to objects. Object definition doesnt declare any instance variable. Instance variables are the variables used freely with the object definition which must be defined in lexically enclosing scope. 

"Ways to postpone plans in E"

(1) Immediate : where previos plan is kept asise and new one is executed , which is more conventional (represented by "."
(2) Eventual (where new plan is kept in 'to-do' list and executed after the previous plan is completed, which is represneted by "<-"

"Simple E execution and VAT"

A VAT is a combination of a normal execution stack, a pending delivery queue and the heap of objects they operate on. It is a minimum unit of persistance, migration, partial failure, resource control, and defence from DOS.

"Communicating Event loops" 
A turn is E's unit of operation.

(1) Near reference : Direct reference between two objects in the same VAT.
They carry immediate-calls and eventual-sends.
Provide one object synchronous access to another.

(2) Eventual reference : Cross boudries and interact with other VATs.
They are first class, they can be passed as arguments, returned as results, stored in data structures, 
Promise Pipelining - Shows that pipelining helps programs to tolerate latency and 
Since each VAT runs concurrently with all other vats, turns in different vats no longer

Among objects in the same vat, E supports `defensive consistency`.
E follows pricioples of POLA (Provide Objects Leaset Authority)

"Promise Pipelining"

It shows handling of return results and exceptions produced by eventual sends.
Promise is an eventual reference. Messages can be eventually-sent to promise before it is resolved. 
Messages sent to the promise cannot be delivered until the promise is resolved,
so they are buffered in FIFO order within the promise.Once the promise is resolved, 
these messages are forwarded, in order, to its resolution.

`Datalock` It is possible to create circular data dependencies which, like deadlock, are a form of lost-progress bug, this kind of bug is called `datalock`.

"Partial failure"

E’s support for partial failure starts by extending the semantics of reference states. E adds a possibility of a vat-crossing reference— "a remote promise" or a far reference — getting broken by a partition. 
The state-transition diagram (a Harel statechart), presents “near” and “broken” as terminal states.
Near references are resolved and local; they carry both immediate-calls and eventual-sends. Promises and vat-crossing references are eventual; they carry only eventual-sends. Broken references carry neither.
Promises may resolve to near, far or broken. Partition may break vat-crossing references
Failures are taken care using handlers. A vat-crossing reference notifies these handlers if it becomes broken, whether by partition or resolution.

A when-catch expression takes a promise, a “when” block to execute if the promise resolves to a value, and a “catch” block to execute if the promise is broken. 

"From Objects to Actors and Back Again" :

This part presents a brief history of E’s concurrency control from Objects, Actors, Vulcan, Joule, Promise pipeline in udanax gold and Orginal E. 

## Analysis

E is an object-oriented programming language for secure distributed computing, created by Mark S. Miller, Dan Bornstein, and others at Electric Communities in 1997. E is mainly descended from the concurrent language Joule and from Original-E, a set of extensions to Java for secure distributed programming. E combines message-based computation with Java-like syntax. <sup>[1]</sup>

This paper focuses on E’s support for concurrent and distributed programming within the constraints of limited trust. Throughout this paper Author describes abstraction mechanisms adequate to craft diverse solutions adapted to the needs of many applications.

A simple example, a “statusHolder” object implementing the listener pattern has been taken to view various scenarios in publisher and subscriber world. 

* Comparisons:
In E as well as Smalltakct and Actors - All values are objects and all computation proceeds only by delivering messages to objects.

* Related work:
(1)Promises and Batched Futures at MIT - asymmetic client server systems. 
(2)Group Membership - provide a different form of general-purpose framework for dealing with partial failure: 
they support closer approximations of common knowledge than E, but at the price of weaker support for defensive
consistency and scalability
(3)Croquet and TeaTime -  a graphical, decentralized, secure, user-extensible, social virtual reality system spread across mutually suspicious machines

Several projects are adapting these insights provied by E to existing platforms.
e.g.
(1) The Web-Calculus, 
(2) Oz-E (capability-based successor to Oz designed to support both local and distributed defensive consistency)
(3 )Twisted Python (This is a library and conventions for distributed programming in Python, based on E’s model of communicating event-loops, promise pipelining, and cryptographic capability security )

* Discussion and Conclusions
At the time when this paper was written despite few successful trials, Authors do not consider E is ready for production use. The current E implementation is a slow interpreter written in Java. 
(1) Encapsulation : separates objects so their plans can avoid disrupting each
other’s assumptions.
(2) Reduced vulnerability helps contain corruption

## References: 

[1] E (programming language) : Wikipedia <https://en.wikipedia.org/wiki/E_(programming_language)>
