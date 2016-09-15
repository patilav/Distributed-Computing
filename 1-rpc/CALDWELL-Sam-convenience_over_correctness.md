In "Convenience Over Correctness" Vinoski attempts to answer the
question why, despite being a legacy technology with well-understood
flaws, remote procedure call (RPC) systems are still popular enough
among developers to enjoy major new developments.

The answer is simple; RPC is easy to use and understand to anyone
already familiar with the language's procedure mechanism.

The cost of this convenience is prohibitive. RPC is like a parasite
that infests a host language's function application mechanism. On the
surface everything looks fine and dandy, but underneath something goes
horribly wrong. As is well understood, RPC lacks facilities for
addressing fundamental concerns in distributed systems such as partial
failure, caching, and logging, among others.

The article concludes with a plea to move towards asynchronous
messaging systems in favor to RPC and laments the accidental
circumstances that historically hindered this move.

In my opinion the article is a shallow critique. I don't think it
offers any new insight into the drawbacks of RPC. The answer it gives
for why people use RPC, because it's easy, is basically the reason
people do every thing they know to be wrong.