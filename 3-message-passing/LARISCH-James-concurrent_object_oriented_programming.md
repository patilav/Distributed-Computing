# Concurrent Object-Oriented Programming
James Larisch

## Summary
  In *Concurrent Object-Oriented Programming*, Gul Agha describes and advocates for the Actor Model. The Actor Model, Agha claims, provides programmers with an effective & easy-to-reason-about model for concurrent for concurrent pieces of software. In the paper, Agha details the Actor Model's primitives, how it holds up against other models (both single and multi-process), and its tradeoffs.

## Outline
### Why is this necessary?
  In 1990, Agha argues that the availability of concurrent architectures is becoming more ubiquitous. Separate processes on a given computer interact more, networked computing is becoming popular, and multi-processor computers are more available. Tie these trends in with the rise of synchronous abstractions such as object-oriented programming and one finds a gap in a useful abstraction for concurrent programming. He alludes to the Actor Model, which he thinks provides encapsulation similar to that found in object-oriented programming, with concurrency. Agha emphasizes that his goal is to detail systems that provide a better concurrency abstraction for the programmer, not static/dynamic analysis tools.

### The Actor Model
#### Overview
  "Actors are self-contained, interactive, independent components of a computing system that communicate by asynchronous message passing" (128). I find it useful to visualize a given actor as a state machine or finite automata, with a few other abilities. The basic operations of an actor are:
  * `create`: creating an actor from behavior description & parameters
  * `send-to`: send a message to an actor
  * `become`: perform a state transition, thus changing the current actor's state / behavior

  An actor, `A`, can be thought of as a given state in a set of states. This state accepts messages of the type `(input, customer)`. `input` is the message/data the given state will handle. `customer` is the actor, B, asking for a response - and where the response will eventually be sent. `A` is free to either spin off new actors, send messages to actors, or state transition.
  Let's say `A` needs to compute two products, then sum those products. `A` can spin off two actors to handle the products. What would this look like? `A` would create an actor for each product computation, sending them the numbers to multiply and `A`'s own address, so they can report the results back to `A` so `A` can sum them and return the sum to the customer. However, this means `A` is stuck waiting for the products to finish computing, and can't handle more requests.
  It would be better if instead `A` spins off another actor, `A'`, which waits for the two product actors `X` and `Y` to finish. Instead of sending `A`'s address to `X` and `Y`, `A` sends `A'`'s address. And since we want the result to be sent back to the original customer, `A` should not send `A`'s address to `A'` - it should sent `B`, the original customer.
  Taking a step back, `A` spins off `A'`, which waits for `X` and `Y` to respond with the products, then reports the sum to `B`. While this is occurring, `A` is free to state transition & handle more requests of the same nature.
  In this example, Agha would call `A'` the join-continuation, since it is in effect the continuation of the outermost behavior of `A`, and is waiting to join two other concurrent events.

  Agha provides a "generator" abstraction, which allows the programmer to specify the exact finite-state-machine transitions for a given "type" of actor.
### Debugging
  Debugging concurrent programs is difficult. Program execution is non-deterministic, the number of objects and interactions is large, and the implications of observing such a system imply a skewed view of the system. Agha argues that actors can remedy this. Rather than implementing an "observer", actors can simply record all input they receive and actions they perform. After execution, an accurate history of what actually occurred can be constructed and analyzed. However, this history would be extremely convoluted. However, due to the nature of actor transitions and calls, it's possible to vary the granularity of inspection. In the above example, it's be possible to trace the request made to `A` and the response given back to it (and where it came from. After that, the debugger could step down `A'`, and from there into `X` and `Y`. Agha refers to these levels of granularity as transactions and subtransactions. Given a transaction, it's possible to step down into the subtransactions that (asynchronously) produced the eventual result.

### Reasoning
  Agha compares the actor model to other, more procedural forms of concurrent programming. In these other models, the programmer is free to use assignment and sequential operations, as opposed to the state transitions found in the actor model. Agha argues that these types of processes become increasingly difficult to reason about, since the are both performing a sequential operation and can accept / deal with asynchronous messages at any time. He compares this type of reasoning to the `goto` statement. `goto` opens multiple (infinite) entry points to a given line of code. Sequential asynchronous processes allow for many entry points and permutations of state for a sequence of actions.
  The actor model introduces no concept of global state. Global state also makes tracking the history of the program unfeasible. Agha compares actors to functions: they are simply traceable state machines that may spawn other state machines. Although concurrent operations are not deterministic, they are easier to reason about than other options.
  Actors can also only send messages to other actors that they know about. If actors could send messages to any actors, this would quickly get out of hand and reasoning about your program would differ little from a concurrent program with global state.

## Limitations
  This is a pretty substantial paper. It discusses a lot of topics while centering around explaining the characteristics and theory behind the actor model. The paper discusses reflection, how object-oriented inheritance plays into the actor model, resource management, and multicomputers. These extraneous details held the paper back. A more concise focus on what the actor model is, how it makes your life better, and more examples of its usage would make this paper more powerful. Also, the paper doesn't really discuss how the actor model should be implemented (or could) in a distributed systems setting. It seems to focus on multi-threaded computers. When giving examples, it talks about concurrency indices. Since it was written in 1990, this is understandable.

## Opinion
  This analysis could have been twice this length. There's so much to discuss in the paper, I tried to distill the pith of it here. I actually wanted to include some examples from the paper but it just wasn't a good idea. That being said, I really enjoyed reading this paper. It was a true struggle at first, and in fact I still don't understand what he's talking about in the "fair merge" section. But it really opened my mind to another model of programming. After getting over the initial hurdle, I really feel like I understand what the actor model is. It's interesting how a normal CS education does not include discussions of other models of computation. It's important to understand historical decisions and possibilities so our minds don't get too constrained to our current ways of thinking. Although it's a dense paper, I'm really glad I worked through it.


