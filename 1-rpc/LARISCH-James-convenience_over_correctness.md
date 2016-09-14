# Convenience Over Correctness: Analysis
James Larisch

## Summary
Steve Vinoski's article titled *Convenience Over Correctness* presents a reasonably modern (2008) outlook on RPC as one of the founding design paradigms for distributed systems. Like *A Note on Distributed Computing*, the article criticizes RPC, specifically regarding its desire for programmer happiness over productivity and solid reasoning.

## Outline
1. RPC Then & Now

 Vinoski describes the origins of RPC: "RPC has influenced distributed systems research and development since the early 1980s." Looking back, however, he notes that many of the earlier RPC-based systems such as CORBA are falling out of favor. However, other companies such as Facebook and Cisco are attempting (in 2008) to provide newer RPC-like systems in order to fill the gap. Vinoski even mentions *A Note on Distributed Computing* - he claims that we've known since that paper (1994) that RPC systems are "fundamentally flawed". The rest of the article seems like a continuation of said paper: more reasons why we should completely stop developing RPC-based systems.
2. Why RPC?

 As per the title of the paper, Vinoski claims that RPC systems developed because they provide a common interface to the user. Users can make local function calls within their local machine and external function calls to machines in the network with the same invocation pattern. The specific RPC-network facilities perform the serialization and communication, unbeknownst to the user.
3. Inconvenient Mapping

 IDLs, or Interface Definition Languages provide contracts for remote objects and functions/methods. Since these IDLs usually appear in languages like Java and C, this often involves defining parameter/return types. Developers often want the freedom to use different languages for different nodes in their systems, so the IDLs are often not constrained to a particular language. Since mapping types from programming language to programming language is not a trivial task, IDLs force programmers to do messy type coercion. Vinoski argues this "results in code that looks neither fully natural within the programming language itself, nor exactly like the IDL."
4. Scalability

 Vinoski acknowledges the points brought up in *A Note on Distributed Computing* such as lack of resilience for partial failures and latency, but brings up a few more shortcomings of RPC with respect to network infrastructure. He claims that intermediary services such as caching, monitoring, and logging are critical for any modern distributed system. He goes on to claim that RPC provides no metadata functionality: since local and remote calls are identical and it doesn't make sense to add that type of information to local calls, this information simply won't exist. Vinoski goes on to say that HTTP makes a great inter-communication protocol for distributed systems. It provides conditional requests, caching functionalities, and idempotence.
5. History & Conclusion

 Vinoski argues that RPC was a mistake. It was a mistake to value developer convenience over "correctness, scalability, performance, separation of concerns, extensibility, and accidental complexity." Erlang provides facilities for making asynchronous calls to other nodes on the network, but they are however not abstractions in likeness to local calls. The developer knows they are making a remote call, but it is in no real sense "inconvenient". Developers should favor languages and frameworks that use more robust and engineered solutions such as HTTP or message queues like XMPP or AMQP. In his final words, Vinoski holds that we must move to better technology or we will make the same RPC-based mistakes we've been making for years.

## Limitations
As far as limitations go, I would have liked to have seen more concrete examples of RPC's shortcomings. It would provide a better picture of where and how RPC fails in the real world. Vinoski always talks about these theoretical programmers and programmatic systems without actually detailing specific systems currently in the wild in which message queues or HTTP would have mitigated some issues they face today. However, it's obvious that Vinoski has written about RPC in past articles and that his knowledge is not by any means shallow.

## Opinion
I think the article has good intentions and provides an excellent viewpoint. As I stated earlier, it seems like more of a "x years later" discussion of *A Note on Distributed Computing*. In 1994 we knew that RPC is terrible, yet 14 years later we need to have another talk about it. Vinoski was right to point out that it is *still* flawed and we should stop designing systems around it. There are better alternatives, like HTTP and message queues. As for the actual points he brings up: I think these are a little weak. There is nothing truly groundbreaking in the criticisms he makes, though the lack of metadata-level infrastructure does make message queues and HTTP sing. The conclusion is powerful and I feel he certainly defended his point, however: RPC should die quickly. As for impact: it's difficult to say whether this paper had a substantial impact or not, but surely RPC has fallen out of favor recently - as a senior undergrad I can say I have had almost zero exposure to it.

