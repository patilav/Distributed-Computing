# "Concurrency Among Strangers - Programming in E as Plan Coordination", Miller, Tribble, and Shapiro
Avanti Patil <patil.av@husky.neu.edu>

## Synopsis

This paper presents problems plan coordination challenges in distributed environment introduced by  (1)concurrency, (2)large latency and partial failure and (3)misbheviour of machines.
E programming language addresses these challenges, by changing few concepts of conventional sequential object programming.


## Summary

This paper focuses on E’s support for concurrent and distributed programming within the constraints of limited trust.
Main contribution:
By dividing the state of a computational system into separately encapsulated objects, and by giving objects limited access to each other, we limit outside interference and extend the range of assumptions
our programs may safely rely upon.
By abstraction, we limit one object’s need for knowledge of others, reducing the number of cases which are
relevant. However, even under sequential and benign conditions, the remaining case analysis can still be quite painful.

Authors provide an example of `The squential "StatusHolder"` for coordinating changing status between publishers and subscribers. This pattern can be used to coordinate a lot of looselu coupled plans but this still creates plan interface hazards like:
(1) Aborting the wrong plan - Imapct of failure of single subscribers resulting in multiple failurers
(2) Nested Subscription - additional subscriptions generated due to other activities and their impact on current subscribers
(3) Nested Publication - resulting in muliple override of data

A Taste of E: 

- Immediate vs Eventual

VAT - Simple E execution- 

Communicating Event loops

Promise Pipelining 

Partial failure

From Objects to Actors and Back Again -  presents a brief history of E’s
concurrency control.


## Analysis

 E is an object-oriented programming language for secure distributed computing, created by Mark S. Miller, Dan Bornstein, and others at Electric Communities in 1997. E is mainly descended from the concurrent language Joule and from Original-E, a set of extensions to Java for secure distributed programming. E combines message-based computation with Java-like syntax. A concurrency model based on event loops and promises ensures that deadlock can never occur. <sup>[1]</sup>

This paper focuses on E’s support for concurrent and distributed programming within the constraints of limited trust.

Throughout this paper Author describes abstraction mechanisms adequate to craft diverse solutions adapted to the needs of many applications.

Example:
with a simple example, a “statusHolder” object implementing the listener pattern.



* Limitations:


* Related work:
Several projects are adapting these insights provied by E to existing platforms.
e.g.

* Discussion and Conclusions

## References: 

[1] E (programming language) : Wikipedia <https://en.wikipedia.org/wiki/E_(programming_language)>
[2] 
