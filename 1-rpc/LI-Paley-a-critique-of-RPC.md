This paper conducts a survey of the various limitations and flaws of the remote
procedure call (RPC) paradigm. The paper introduces RPC, and goes onto claim
that RPC are commonly misused partly due to it’s popularity and fashion (within
that time period).


Tanebaum et al. splits the flaws of RPC into four categories: conceptual issues,
technical issues, issues caused by operating over heterogeneous machines, and
performance issues.


RPC is a typical request-response message passing system that values location
transparency. The RPC model for a client procedure calling the server is divided
into four stages. First stage, the client procedure calls the client stub.
Second stage, the client stub marshals the parameters and calls the server.
Third stage, the server stub un-marshals the message. Final stage, the server
stub calls the server procedure. The same stages are reversed for when the
server returns the client’s call.


Tanebaum et al. presents many of the issues regarding RPC by comparing it
against the virtual circuit model, although they clearly stated that they do not
advocate for this model due it’s own shortcomings.


The majority of the flaws with RPC described are due to the request-response
nature of RPC, the transparency requirement of RPC, or the innate difficulty in
identify which part of the use case is the client and which is the server.


A common problem with RPC is when a message sent over the network is lost, or
when additional messages or informations are required to be sent over the
network. For example, if a message is lost, either from calling the server or
returning the call to the client, then neither the respective server nor client
would know that their message has not been received. Losing a message arises
issues such as the lost of the server state and the possibility of repeating
many execution of client calls.


The underlying server and clients model of RPC is not ideal for every
distributed situation. The server must reply whenever a client has called the
server, even when a response is not expected. Furthermore, the client cannot
proceed with its computation until it hears back from the server, even if the
server has already computed parts of the entire message.


There is no easy way to parallelise the computation in the server with those in
the clients. Similarly, a server cannot be ran on multiple threads nor can it
reply back to multiple clients at once. The use of global variables and object
pointers within the parameters marshalled to the server are problematic, as well
as using dynamically typed languages.


Further issues could arise when the client and server are on different hardware,
such as a loss of precision in the parameter representation, or memory storage
differences between different machines.


Overall the paper present a detailed collections of the limitations in the RPC
model, along with an example for each limitation. The authors does not present
any solutions, alternative, or fixes to the problems they’ve described. Through
some of their examples it is easy to see that if the RPC model was missed used
then it would not work very well. The tone and main takeaway I’ve gained through
this paper is that the RPC model is not the be-all end-all model of distributed
system, which may have been the popular opinion upon the time this paper was
written.


It would be interesting to explore if a small change to the RPC model could
solve any of the issues listed, or would a separate new domain specific model be
required. I wonder how many of the issues described in this paper affects state
of the art distributed models, or if this was the paper that created the
blueprint for future researchers to solve.