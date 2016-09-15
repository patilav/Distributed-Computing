# Implementing Remote Procedure Calls

## Summary

Implementing Remote Procedure Calls (RPC) discusses the design and
implementation of RPC circa 1984. While RPC did exist prior to this
paper, [1,2] this paper appears to be the first to format into
a concrete package that includes both the design and implementation of
an RPC system for a Xerox machine.

The API proposed by this paper is roughly similar to that of a
traditional function call. Specifically, the caller machine waits as
until the callee machine returns a result. This relies on an external
threading mechanism to get any form of concurrent execution. A forking
API could easily be emulated and does not fundamentally change the
design.

The protocol also relied on a centralized database to map which remote
machines can handle which procedures. This allows for a more dynamic
assignment of machines then being baked into the caller's program,
while also saving a lot of useless network traffic that would be
required to have callee's register to receive functions. Obviously
this database would become a bottleneck for RPC, but the paper does
not discuss it.

The paper does go into detail about several other problems that occur
in RPC, and describes how their implementation handles them. These
include security concerns when transmitting data from one machine to
another, loss of packages when sending and receiving data, callee
machine crashes, and computation exceptions.

## Thoughts

This paper is a good demonstration on how not to write a design or
implementation paper today. While applicable at the time of
publication, many of the details of this paper are fairly useless to
the reader. This goes beyond the now outdated technology the paper
used, and includes a large amount of buzzwords in the paper that are
not relevant. For example, this machine had a lengthy section
describing the branch of machine they were running, and another one
describing the brand of database they used. While the (abridged)
technical specifications for the machine and database would be
appropriate for this implementation paper, the actual brands are no
longer relevant and do not give any clarity to the reader.

Other than these basic writing failures, the paper is an interesting
read given the time it was published. It certainly did not cover all
of the issues that will arise in RPC, but did cover many of the big
ones. One major concern that did seem missing to me was what would
happen when the procedure call causes the callee machine to crash. The
paper seems to handle the case of a procedure causing an
exception. However this relies on all procedures running in a safe
language or environment. Even if RPC is used exclusively in a safe
environment, bugs will inevitably happen, and the paper lacks how
their RPC protocol will handle that. In the case of local procedure
calls undefined behavior occurs, frequently leading to a segfault. In
the case of RPC, undefined behavior will still occur, but it seems
like it is more likely to infinitely loop.

## Local RPC

One interesting thought that I had while reading this paper was that
this protocol looks similar to how I would design a local RPC
protocol. This initially appears to be silly and adds a lot of
overhead. But it seems like a useful way of creating parallel (but not
necessarily concurrent) code. Additionally, many of the potential
issues of RPC (such as packet loss) are much less likely to occur when
run on a single machine. The remote part of RPC in this case would be
different processor cores, which is relevant on increasingly multi-core
machines.

## References

[1]: Remote Procedure Calls, Tech Report

[2]: A high-level framework for network based resource sharing
