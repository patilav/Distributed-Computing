<meta charset=utf8>

# “Multilisp: A Language for Concurrent Symbolic Computation”, Robert H. Halstead; ACM TOPLAS 7(4), 1985

Tony Garnock-Jones <tonyg@ccs.neu.edu>

## Synopsis

The author describes the design and implementation of Multilisp, a
dialect of Scheme enhanced with a novel `future` construct for
introducing parallelism into an otherwise-sequential program.
Multilisp as a whole is placed within a taxonomy of parallel
languages, and the `future` construct specifically is compared to
other forms of delayed evaluation.

## Summary

The paper begins with a discussion of issues in parallel language
design, proposing a taxonomy based around three binary characters: the
explicit inclusion of parallelism in a language's semantics; the
inclusion of side effects; and the assumption of a shared memory
model. The taxonomy is employed to classify a few languages into four
of the available eight combinations of characters. Multilisp is placed
firmly within the category of explicitly-parallel, side-effectful,
shared-memory languages, alongside peers such as Concurrent Prolog and
Ada.

The paper motivates the decision to occupy this portion of the design
space by making an argument in two parts. The first claim is that
without explicit parallelism, automated discovery of opportunities for
parallel execution is limited when side-effects and shared memory are
present, reducing coarse-grained parallelism. The second claim is that
avoiding side-effects and shared memory makes fine-grained parallelism
too hard to use effectively. Together, these imply that a language
aiming to support a wide range of *scales* of parallelism must include
all three characters.

A significant theme of the paper, first appearing as part of the
exploration of design alternatives for parallel languages, is
criticism of the "Communicating Sequential Processes" (CSP) paradigm,
as applied to parallel programming. The core of the critique is that
the isolated stores of CSP processes force the programmer to build
their parallel systems as if they were distributed, even in the
non-distributed case. The "nonuniform style of access to data" that
results forces the programmer to design protocols for nonlocal access
to data, leading to a "psychological disincentive" which "strongly
discourages" fine-grained parallelism in a system design. By contrast,
it is an explicit goal of Multilisp to "minimize the extra cognitive
load on the programmer that results from the introduction of
parallelism".

Having discussed design options, the paper turns to a description of
Multilisp and its novel features. First, it touches on the main points
of Scheme, the sequential language underlying Multilisp. Of note is a
remark that Scheme's lexical scoping, at the time somewhat
controversial in Lisp circles, is particularly important in a parallel
setting, since alternatives such as dynamic scoping are difficult to
implement efficiently when multiple tasks may have different views on
values of "the same variable". Scheme's "first-class" procedures form
the core of Multilisp's approach to data abstraction, and its powerful
side-effect-free subset leads to a largely 'pure functional' style of
programming that fits well with the introduction of parallel
constructs.

The paper first describes the parallel construct `pcall`, which
evaluates all subforms in parallel, waiting for them all to yield
values before combining them into a function call. It is effectively
an implicit fork/join construct, and is macro-expressible [1] in terms
of the central novel feature of Multilisp, `future`.

The `future` construct begins evaluation of its single subform in
parallel with the rest of the program, yielding a *placeholder* value
(a *future*) to its continuation. The placeholder is transparently
replaced with the result of the background evaluation when it
terminates. If, at any time, a task makes a control flow decision
based on a future, its execution is paused until the future's
background task completes.

After analyzing an example `quicksort` program in detail, the paper
places `future` in context alongside other delayed evaluation
constructs, sketching an outline of a taxonomy of such constructs and
using the comparison to highlight the termination behavior and
intended use of futures; namely, that `future` is intended only for
values that are *certainly* needed by the context, and for expressions
that are *certainly* terminating.

The paper compares the design of Multilisp to other multiprocessor
Lisp designs before presenting details of its implementation,
including a description of the compilation approach, of
synchronization constructs, of the interactions between futures and
the evaluator, of task management and scheduling, and of the Multilisp
approach to parallel garbage collection. Finally, the expected
parallel speedups of Multilisp running an 'embarrassingly parallel'
program, the `quicksort` implementation analyzed earlier, are shown.

## Analysis

The central contribution of this work is the notion of `future` as a
construct for adding low-cognitive-overhead explicit parallelism to an
existing sequential mostly-functional language.

As such, it is a contribution to the literature on constructs for
*parallelism*, and does not address issues faced by *distributed
systems*. This is clearly visible in the critique of the CSP model,
which rests on an assumption of the existence of an alternative to the
strong isolation of stores in CSP. In a distributed system there is no
such alternative. Shared memory in a distributed system must be
simulated by a message-passing protocol between strongly-isolated
domains.

However, each component in a distributed system almost always faces
issues of coordinating concurrent activities, and these activities,
when distributed, may in turn entail global parallelism. In this way,
distributed systems demand adequate support for concurrency in their
constituent sequential languages, and we may consider `future` as a
candidate building-block. Perhaps a language hybridizing
Multilisp-style ideas with features drawn from the CSP model would be
able to span the divide between local and distributed concurrent
programming.

Many of Deutsch's "fallacies" [2] cease to be directly relevant when
viewed through the narrow lens of local programming constructs such as
`future`. Two that remain are *latency* and *reliability*.

While futures do not offer the programmer much in the way of control
or insight into latency, they can help *hide* latency by making a
placeholder value immediately available. In the case of futures
involving communication, latency of communication is blurred together
with latency of computation. Later research on *batched futures* [3,4]
has explored some of the ramifications of this idea. Futures
effectively decouple producers and consumers of values in both space
and time: this characteristic is shared by *message queues* widely
used in distributed systems architectures.

Turning to reliability and failure, the current paper does not go into
detail about error handling or exceptions in the presence of futures,
pointing out only that further research on error and exception
handling is needed. One important issue is the scope of exception
handlers: if a future raises an exception, which exception handler
should be activated? The handler in place at the time of *creation* of
the future, or at the time of its *use*? How should the context of the
error be reported to the user? In this way, error handling in
Multilisp suffers from design challenges similar to those of error
handling in lazy languages. In both cases the scope of the relevant
side effect is unclear.

When futures are used to represent the results of distributed
interactions, an additional problem arises. Computations guaranteed to
terminate in a local setting may no longer reliably yield a result
when partitioned across a less than perfectly reliable network.
Multilisp offers no means of cancelling a future once it is started,
making it difficult to 'give up on' a distributed computation when a
program suspects that network conditions will prevent it from ever
completing.

Finally, the paper poses one or two open questions that have been
resolved by subsequent research. For example, the problems that arise
when combining full continuations with futures have been largely
resolved by the development of the theory and practical use of
delimited continuations. Additionally, the three-character taxonomy of
programming languages is neither convincing nor fully explored:
unconvincing since "shared memory" is more an implementation
consideration than a part of the computational model; and not fully
explored since only four of eight possible combinations are discussed.
Languages such as Erlang [5] might fit in the explicit-parallelism,
effect-free, non-shared category, while languages such as CML [6,7]
and Haskell might fit in the explicit-parallelism, effect-free,
shared-memory category.

## References

[1] M. Felleisen, “On the Expressive Power of Programming Languages,”
in Proc. ESOP, 1990.

[2] <https://blogs.oracle.com/jag/resource/Fallacies.html>

[3] P. Bogle and B. Liskov, “Reducing Cross Domain Call Overhead Using
Batched Futures,” in Proc. OOPSLA, 1994, pp. 341–354.

[4] E. Tilevich, W. R. Cook, and Y. Jiao, “Explicit Batching for
Distributed Objects,” in Proceedings of the 29th International
Conference on Distributed Computing Systems (ICDCS 2009), 2009.

[5] J. Armstrong, “Making reliable distributed systems in the presence
of software errors,” Royal Institute of Technology, Stockholm, 2003.
(PhD dissertation.)

[6] J. H. Reppy, “CML : A Higher-order Concurrent Language,” in ACM
SIGPLAN, 1991, pp. 293–305.

[7] J. H. Reppy, Concurrent Programming in ML. Cambridge University
Press, 2007.
