# Concurrent Object-Oriented Programming: Analysis

Nathaniel Dempkowski

## Summary

Concurrent Object-Oriented Programming focuses on the intersection of two trends of computing: increased distributed computing resources and the rising popularity of object-oriented programming. The paper defines common patterns of parallelism: pipeline concurrency, divide and conquer, and cooperative problem solving. It then focuses on how the actor model can be used to solve these problems in an object-oriented style, and some of the challenges that arise with distributed actors and objects, as well as strategies and tradeoffs for communication and reasoning about behaviors.

## Outline

The paper starts by discussion the trends of concurrency in computing. It mentions three major ones: increased use of interacting processes, cost-effective workstation networks for resource sharing and distributed problem solving, and lower-cost multiprocessor technology which provides supercomputing power. These trends, along with a need for data abstraction satisfied by the rising popularity of object-oriented programming naturally overlap. The paper argues that concurrent object-oriented programming (COOP) provides a solid foundation for concurrent computing on multiprocessors.

Concurrency provides flexibility to interleave execution, leaving you free to distribute the work as you see fit. This allows you to abstract away details and focus on conceptual issues. The paper defines an object as an entity encapsulating data and operations. This means that models of concurrent computation that are based on objects specify how they interact, and that different design concerns lead to different models of communication between objects.

Common patterns of concurrent problem solving can be expressed in COOP structures. The actor model is of particular interest, and some of the OOP concepts are useful in building actor systems. Actor languages give special emphasis to developing flexible program structures to simplify reasoning about programs. Additionally, there are some other models of objects, so the paper details later how they relate to the actor model.

The paper defines three common patterns of parallelism: pipeline concurrency, divide and conquer, and cooperative problem solving. Pipeline concurrency is an enumeration of potential solutions to a problem, with concurrent testing of solutions. Divide and conquer is the concurrent evaluation of independent subproblems and the joining of results of those subproblems. These problems are often expressed as functions where arguments to a function are evaluated concurrently, and values are subsequently collected. Cooperative problem solving is an interconnection network of objects, where intermediate results are stored in objects and shared by message passing between objects.

The paper then focuses on the actor model, which is allows us to semantically view behavior of objects as functions of incoming communications. Actors are self-contained independent components that communicate via. Asynchronous message passing. Three actor primitives are offered:
* `create`: this creates an actor from behavior and parameters
* `send to`: this sends a message to an actor
* `become`: this allows an actor to replace their behavior by another, which allows for history-sensitive behavior necessary for shared mutable objects

Actors have state changes specified using replacement behaviors. They process communications and computer behaviors in response to the next communication. The paper argues that Specifying replacement behaviors is an advantage over convention assignments as it allows aggregation of changes at a higher level of granularity. It also allows for easy determination of when a state computation has been finished and it is time for the next interaction. This guarantees noninterference of state changes, even with potentially numerous threads for a single actor (under a multiple reader, single writer constraint).

Actors can define history-sensitive behavior, which the paper argues is necessary for an actor to change local state and respond to multiple message types. This can be done by defining a generator actor which creates other actors using a behavior template. These actors can use operations to pass along keywords which determine different behavior. In this model actors essentially pass a message specifying the type of behavior and arguments required for that behavior. This allows for the object-oriented message-passing style and the function style of programming to be mixed in an actor language. An implementation detail here is that computation of replacement behavior can be done concurrently with the computation of the response.

As many of the problems that actors are used for involve aggregating results, there is a need to create join continuations which specify how the results of subcomputations across actors should be aggregated. These enable divide and conquer concurrency, and additionally define error handling that can be passed back and aggregated.

Next the paper addresses nondeterminism in concurrent computing, and the need for synchronization, which requires the ability to merge messages sent to an actor. They describe the semantics of a fair merge in which messages are merged from all senders (meaning one can’t be indefinitely ignored) in no particular order. They believe that this enables abstraction of process scheduling, and execution speed which are generally just architecture specific details. The fair merge also enables reasoning about the eventual behavior of a concurrent program. Fair merges are implicit in the actor model, and are captured by the guarantee of message delivery with a finite but arbitrarily long delay. However, this really can only be provided with some bounded level of confidence in a well-engineered system and must be implemented using some sort of timeout.

Another point it addresses is the need for synchronous communication in actor systems. Because there is no instantaneous action at a distance in a distributed system, this synchronization can only be implemented by constraining implementation of synchronization with a time bound. This is critical for coordination problems, as it enables implicit/common knowledge. This shared knowledge can be used for transactions which act as delineations of error recovery or resource allocation boundaries.

The paper also addresses difficulty in visualizing actor programs. It is hard to monitor and debug them, as they are nondeterministic and have large numbers of objects and interactions. This results in an enormous set of events and relations. Additionally, there is no true global state, as that is dependent on the observer object’s frame of reference. Their solution to this is to define transactions, which indicate the events between a request message and a response message. These transactions obey three properties:
* You can have subrequests/subtransactions nested within a transaction, and these are the requests made during that transaction.
* Subtransactions cannot be shared by multiple transactions.
* The above conditions are recursively held for subtransactions.

These properties mean that transactions can be used to pinpoint errors by focusing recursively on smaller areas of interest.

Resource management is an additional concern in actor systems, as at some level you are limited in the amount of parallelism you have within your system. In partitioned problems you need to be able to specify how you want to allocate resources dynamically to subproblems. An example of this was a graph search problem. You would want to know to what extent you should explore neighbors of a node based on some relevance metric. This suggests the need for a “sponsorship algorithm” which gives you a high-level control over the relative rate of unfolding computational paths.

The paper also addresses some of the tradeoffs in other objet models, specifically one which takes a procedural view of objects as sequential processes which respond to messages. Here every object can operate concurrently, but they are limited by a concurrency index, which is the number of objects that have a pending message to process at any given time. This sequential model has disadvantages. It is not optimal across architectures, it is harder to reason about state changes and parallelism within the model due to the logic split, and it uses assignments of values to variables instead of functional components.

The paper finally talks about how to reason about behavior within concurrent object oriented systems, by making the point that it is generally impossible to determine precisely what state components will be in when communicating. They say that encapsulating operations and making intermediate states invisible to the outside prevents actors from being interrupted. The behavior of an actor is atomic, so interaction problems get transferred to a different level of abstraction. There is still the issue around large possible numbers of computations due to messages from different senders, but this is mitigated by two factors: the encouraged use of referentially transparent functional components and the customer-passing style which separates continuation of behavior from continuation of computation. One other factor that makes reasoning more possible is the locality of messages. Objects may only send to objects they know about, and locality laws (axioms governing which objects are known to another object) make this feasible.

## Limitations and Extensions

One question I had when reading this paper was regarding a purely functional actor model. The paper pointed out a need for mutability and history-dependent behavior, but I never really saw the reasoning behind it. It seemed like the examples they described which “required” mutability could just as easily have been replicated in a purely functional model, as long as there is some functional equivalent to `become`. It would be interesting to extend this to an entirely functional actor model.

Another thing that stood out to me was the amount of time spent defining different ways of synchronization. They want to focus on message passing, but most of their usages of it almost just feel like function calls that would benefit from some sort of explicit future-based synchronization. Maybe the idea of futures wasn’t so popular, but it seems that introducing futures and explicit function calls might be a better solution to some of the synchronization/shared knowledge problems.

## Opinion

I think that the actors described do really sound like Alan Kay’s original vision of object-oriented programming, rather than what I would call “modern” object-oriented programming where a lot of the focus is around hierarchical organization of structures and information. When you program in Java for example the focus is around storing information and making method calls. You aren’t doing this style of implicit concurrent message passing between individual units of computation.

This idea of message passing between individual objects of computation concurrently seems like a reasonable model for separating concerns of objects, though doing this with implicit concurrency probably raises a lot of concerns that weren’t addressed in the paper. They basically leave a lot of the issues to either “don’t exceed the capabilities of your hardware” or “use timeouts everywhere” and I don’t think those are the most practical ways of dealing with those issues. I think that any system that implements these ideas probably needs to have better solutions than those around scheduling and timeliness for anyone to use it in practice. The concurrency index was a particularly glaring example where you felt like you would need more information like a hierarchy or a thread pool or something to manage scheduling and resource allocation for any non-trivial task.

Additionally, I would have liked to have seen better examples of problems or systems that the actor model solves elegantly in this paper. They mentioned a few examples of systems that had some concurrency problems, but there wasn’t really a true comparison of solving those problems with another concurrent programing model.