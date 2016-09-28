# Your Server as a Function
James Larisch

## Summary
In *Your Server as a Function*, Marius Eriksen describes Twitter's Finagle runtime library, which exposes three programming abstractions for effective and concise concurrent programming. Eriksen goes on to describe these abstractions (Futures, Services, and Filters) in depth, as well as their implications and drawbacks for the engineering organization at Twitter.

## Outline

### Specification & Usage
#### Futures
  Futures are defined as a "container used to hold the result of an asynchronous operation". As a type, futures are defined as `Future[SomeType]`. A future may be either `Future.value(something)` or `Future.exception(something)`, which represent a successfully returned future value or an exception which arose within a "chain" of futures, respectively.

  Futures can be "unwrapped" with the `flatMap` combinator. By calling `flatMap` on a given future `Future[A]` and providing a function of the type `A -> Future[B]`, one can chain asynchronous operations. `X.flatMap(Y)` will perform `X` asynchronously and once completed will perform the function `Y` with `X`'s resulting inner value.

  If an error occurs in a future computation, the future "chain" will short-circuit and immediately return a `Future.exception`. The `rescue` combinator is like `flatMap` except it is called on exceptions.

  Eriksen discusses how often times multiple servers, for example, need to be queried and the results combined. The `collect` combinator is useful here. It collapses a sequence of futures into a future of sequences. The returned future will return once all of the original input futures conclude.

  Eriksen discusses how recursion is idiomatic: it's acceptable to call Future-returning functions within Future-returning functions. He mentions that `flatMap` does some kind of tail-call optimization.

#### Services & Filters
  Services are simply asynchronous functions of the following type: `Service req resp = req -> Future[resp]` "Servers implement services to which Finagle dispatches incoming requests; Finagle furnishes clients with instances of `Service` representing either virtual or concrete remote servers."

  Eriksen mentions often times "application-agnostic" functionality is desired. This sort of modularity takes the form of filters, which are essentially service-modifiers. A filter's type is `Filter req resp = req -> resp -> Future[resp]`. The example Eriksen provides is a `timeout` function which takes in a timeout duration and returns a filter which applies the timeout to a given service. Filters can be composed with other filters or services using the `andThen` combinator.

  These can, for instance, force HTTP requests to be authenticated (and thus be represented as a different type) before being sent to a service that requires an authenticated request. If authentication fails, the flow short-circuits anyway.

### Discussion

#### Benefits
  As Eriksen points out, expressing patterns such as authentication as typed-flows provides compiler-level safety. This leads to this set of abstraction's main selling points: creating composable, concise asynchronous flow "paths" leads to an easily understandable and visualizable programming experience.

  It's a "data-oriented" style, which means the programmer is focused on what's coming in and what's going out, rather than the specific details. Eriksen also notes that it's easy for programmers to notice "heavy" chains of computation just by glancing at the steps being "chained" together.

  Immutability obviously provides a high-level of safety as well: data is simply moving, not being modified in a shared place.

  Since consumers of this runtime library don't worry about the lower level internals of what's going on, the maintainers of the library can focus on optimizing under the hood. Eriksen touts Finagle's ability to implement thread affinity and other I/O customizations. They can also add Dapper-style instrumentation and trace requests through various Services and Filters.

#### Drawbacks & Obstacles
  As for drawbacks, Eriksen admits that there is a rather serious problem with, for example, the timeout functionality. Since timing out simply stops the library consumer from listening for a response, the actual concrete action (say, an HTTP request) may continue to occur. In order to combat this, they implement an "interrupt" functionality which actively walks up the dependency graph and sends terminate commands.

  Eriksen also talks about garbage collection and space leaks. Since closures need to be heap-allocated and might not terminate for a while, they may not be garbage collected until a major GC occurs. He says that small GCs are one of the most common server delays, especially when a server makes a "fanned-out" request: the slowest server is the limiting factor.

  Even more seriously, it's possible that a long-running closure holds on to a large and dead reference. This can cause serious space leaks.

## Limitations
The paper does a great job of explaining the API Twitter has created and how to use it. They claim it makes their engineers more productive and that it makes the code more concise. They even provide a code sample to show an example service using a few filters. I think it would have boosted Twitter's argument if they had provided some before-and-after code samples. Exactly how concise does this new style of programming make existing code? How long is the migration time? Onboarding time? Those who tout functional programming often talk about how much easier it makes the programming experience, but they often fail to provide concrete examples. That being said, I realize this paper is more of an introduction to the system as a whole rather than an analysis on its efficacy. However, since they discuss some rather serious drawbacks (GC, interrupts) it would be nice to see tradeoffs discussed, and whether they've found the experience worth it. Especially considering FP is gaining some steam and companies are concerned about whether functional styles will actually provide measurable benefits.

## Opinion
I am not particularly well versed in the history of futures as a concept and their implementation. Eriksen introduces futures in this paper as a relatively novel concept - and since he spends so much time discussing their usage, I think it's safe to assume such patterns were not commonplace at the time of publication. With that under consideration, I think this paper is extremely important. Functional programming, not just with respect to the language but with respect to the mental model of designing systems, does create an easier-to-reason-about programming methodology. It's fantastic to see this model picked up by an influential organization and outlined here. They admit it has made understanding their services a better experience (although they don't explain specifically how they know). As someone who was already familiar with this concept reading the paper in 2016, however, I find myself much more curious about how the runtime library was actually implemented, since I think almost all modern languages should provide support for this type of programming. In my opinion this style of programming is vastly superior, and I sincerely appreciate Twitter's contribution to this functional "movement".
