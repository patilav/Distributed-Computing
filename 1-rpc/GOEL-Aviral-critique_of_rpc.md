# A Critique of the Remote Procedure Call Paradigm

Authored by *Andrew S. Tanenbaum* and *Robbert van Renesse*
Summarized by *Aviral Goel*

---
    
## 1. Summary

The paper criticizes RPC as a general communication model in distributed systems by evaluating its behavior with respect to client server crashes and system heterogeneity and describing its negative impact on performance and architecture of distributed systems. The authors conclude that true transparency, as embodied in the design of RPC, is impossible. They claim that a completely non transparent model is also not suitable. Whether a partially transparent model will solve these problems is left as an open question to the readers.

## 2. Details

The authors describe the following problems with RPC -

### 2.1 **Conceptual Problems**
RPC is inherently a two party blocking interaction with no scope of multicast or broadcast. Moreover, certain computations require the processes to behave both like a client and a server and RPC is inappropriate for such systems. RPC provides no mechanism for the server to initiate communication with the client nor does it allow the same server procedure to be called a second time before it has returned the first time.
It also does not provide completion guarantees in lossy networks. Solutions to some of these problems are ad hoc and impose major design decisions on the programmer, thus restricting their freedom.

### 2.2 **Technical Problems**
Parameter marshalling is not easy for procedure arguments in non type safe languages. It may also be difficult to determine which union member or variant record is being passed if union types or variant records are allowed. Pointers pose a similar challenge. References to remote global variables are also difficult to deal with. All known solutions to these problems have drawbacks. Furthermore, due to network delays, a remote call issued between two consecutive timing sensitive commands may result in the corresponding operation to time out, thus making RPC unsuitable for such systems.

### 2.3 **Abnormal Situations**
If the server crashes, long term state information is lost leading to indeterminate behavior, especially in the middle of a non idempotent operation. Client(s) can hang forever waiting for response or try a few times and throw an exception where logically no exception is expected. If the client crashes, server's computation becomes an orphan. Elegant methods of orphan elimination are not known. Virtual circuit systems do not suffer from this problem.

### 2.4. **Heterogeneous Machines**
Floating point representation, byte ordering and struct field alignment differ with machine architecture. Without some form of option negotiation in the beginning, parameters marshalled by one process will be undecipherable by the other. Transparent RPC does not provide any provision for such negotiation.

### 2.5. **Performance Problems**
With RPC, the client waits idly for a response from the server after issuing a remote call. With other models it may be possible for the client to continue working. There is also no provision for the server to stream results. Making remote calls look like local calls can also trick the programmer into making bad assumptions like running a small important procedure remotely, affecting the overall performance heavily.

## 3. Achievements

The paper successfully demonstrates the shortcomings of RPC through well organized problem categories and numerous realistic examples.

## 4. Criticisms

The authors do not discuss an alternative to every problem encountered due to the design of RPC. For some problems the virtual circuit model is compared to RPC. This begs the question if the remaining problems are inherent in the design of RPC or will they still manifest in some form in other designs. It is also claimed, without suitable examples, that a completely non transparent model is inappropriate. The question of a good model for distributed systems is left unanswered without useful hints.

## 5. Related Work

Many experienced practitioners have written about the issues associated with RPC. The virtual circuit model tried to correct a few of these problems but failed on many accounts. Many RPC libraries exist and RPC based models are widely used in a variety of distributed systems. The paper is quite old and new models such as pub-sub pattern using message queues have come up since then which have improved on many of the aspects of RPC. Today's designer has plenty of communication models to choose from based on his requirement.
