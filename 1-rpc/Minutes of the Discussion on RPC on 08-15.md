# Minutes of the Discussion on RPC on 08-15

### Opening remarks
The idea behind this discussion was to collect the opinions, criticism, issues and critiques about all the papers which were presented before this discussion.

Professor starts with an opinion that in the critiques paper, they claim that RPC is bad, because it doesn't fully support all the features of some programming language. Is it wrong to assume that RPC should support every bit of some programming language or something else is wrong ? Maybe the idea of RPC is less to blame and expectations are set incorrectly, or presented in a context which is not valid anymore.


Tony mentioned that in the original paper, they developed their own transport instead of using say TCP. In contrast, later systems used TCP. So, a way to look at RPC would be to look at the aspect of RPC which are different from concerns with transport.
Just looking at architecture of RPC would be better to judge RPC.


A mentioned that the original idea that there would be a local network underneath the surface actually colored the way they thought about transport and later on, as the network became bigger, something like TCP would have been lot more appealing than the original protocol.
Tony and A also mentioned that there would have then been more datapoints to compare the original results with others.

Leif and Professor observed that that the first paper had too much details on the transport protocol than the actual RPC.

Professor then presented the idea that RPC was originally presented in a context which isn't valid anymore, and the criticism on RPC were mostly due to different expectations. Also, it was weird that they started out looking for a system which is five times slower than regular, but even though the performance was 1000 times slower, they still said it is good performance.

Leif then brought up his old point about "processors on a die" analogy, and according to him, the ideas in the original paper starts to make a lot more sense when considered on a local machine instead of the internet.

Tony made a point about the original RPC model being similar to systems like DBUS where the stuff is on a local machine and not really distributed.

Leif made an argument that the stuff on a local machine can be considered to be distributed, and the various components making a local system can and do go down.

Kisalaya and Tony agreed to the point that the local systems are different from distributed systems because of presence of the Operating System as sort of a reliable way of knowing what has exactly gone wrong. 

Kisalaya made a point that this lack of a central coordinator as a way to reliably know what has gone wrong was one of the criticisms in notes paper.

Leif and Tony made an observation that the operating systems are heading into the direction of distributed operating systems. Tony was curious about the role of this central coordinator in this sort of a scenario. Kisalaya answered that the paper only stated the problems in an abstract way, and had no specific role in mind for the coordinator.

Aviral raised a question about how the things work on the cloud. How does it become more reliable because of the central coordinator. 

Steve quipped about how reliablilty of cloud is linked to the reliablitly of the hypervisor.

Paul and Professor made a point about the services for monitoring and management. Tony mentioned about the idea of availibility zones. It is interesting because it says the whole data center comes and goes as a unit, and there are more than one datacenters. Datacenters are made of many individual machines, and machines are made of atomic processors. So this can be thought of as a recursive layering, where the smaller you are, faster and more reliable you are. Then you go a level up, it gets slower and less reliable and so on, till we get to whole planet.

Paul mentioned about process migration where we don't see a process die because a dying is journaled and restarted at another location. 

Muzzamil raised a question about why would we use RPC instead of REST APIs.
According to Paul, REST APIs can be endpoints for RPC calls. On Google Api explorer, we can see which of google apis are actually RPCs.

Tony disagreed to the above point of REST being endpoints for RPCS. He quoted the guy who came up with REST was very clear about REST not being RPC and it cannot be RPC. Paul mentioned that this idea is very old, and now REST can be RPC. This is a terminological drift in definition of both RPC and REST.

Muzammil followed up with a question about usefulness of stubs. Paul made a point about not having to create channels again, which is an expensive operation. Professor mentioned that today, RPC helps connect servers and clients written in different languages. Stubs can be written in IDL and gets generated, rest of the code is written in whatever language. 

This discussion led to talking about finagle. Finagle is built only for JVM, java and scala. It feels like a part of the language, with system and futures. It does away with defining message types and implementing methods that should exist. This is essentially composing RPC calls in a functional way. This was Professor's view of no-stub, and the common runtime is what enables this.


Muzzamil asked a question about the difference between RPC and REST. 
According to professor, RPC these days means anything where you have a server and a client, and you're sending something to the client in a request-response pattern. Taking away the stubs ant all the other details, this is all they mean. So there is no guarentee that it looks like invoking a function, although it implicitly comes with RPC.

According to Tony there are two things that distinguish classic RPC from what it is now. 1:"It is just a protocol now" and 2:"People have given up on transparency". 


Tony then asked whether HTTP is RPC. This was an interesting observation,because even though HTTP looks a lot like function calls, now we're confusing between RPC and the transport layer which enables it. 
Tony made a point about some RPC systems supporting streaming along with the request-reply, and HTTP does both as well.
B talked about how HTTP is way to get data, without thinking how you're getting it.
Aviral: "RPC is a higher level abstraction over HTTP?"
If we're not defining the type of communication, then we're essentially just sending bytes over the wire without any interpretation. HTTP is simply a way to transporting data, and we need to define an abstraction over what those bytes actually mean.

### Conclusion

One obvious takeaway from the discussion is the shifting definition of RPC. Even though difining the shift accurately is hard, it can be viewed as either widening of it to general principals or narrowing down to a pure version of it. And according to Professor, a definitive arc on RPC should be able to charecterize this change in the definition.

Aviral made an anology about how every language implements Object oriented concepts in a different way, but all are still object oriented and how RPC defined is defined in many different ways.

Kisalaya made an observation about how RPC today can also be used to glue together a system written in many different languages. Aviral talked about how this was the goal for IDL. Professor added that this wasn't the original goal but it is being used that way now and how RPC makes microservices possible. Leif made a point about thinking of RPC as a FFI. Tony confirmed that LuaFFI is actually using RPC.







