#A Note on Distributed Computing

##Summary

This paper demonstrated that the unified view of local and distributed objects is mistaken, and there are fundamental differences between the interactions of distributed objects and the interactions of non-distributed objects.

##Main points
There are three main parts of this paper, namely, what is the vision of unified objects, why the vision is false and what are the real world examples that can demonstrate the authors points.

In the first part, the authors explain that the unified vision is centered around the principles that a.there is a single natural object-oriented design of a given application b.the failure and performance issues are tied to the implementation c.the interface of an object is independent of the context. 

In the second part, the authors demonstrate that the major differences between local and distributed computing--latency, memory access, partial failure and concurrency make the unified vision impossible without making unacceptable compromises. Although the authors admit that latency and memory access differences are possibly being masked in the future, because of the lack of central view of a dstributed system, the gap between partial failure and concurrency can not be masked without certain sacrifice(i.e. by introducing uncessary guarantees).

In the third part, the authors introduce the example of NFS and show that the unified vision of the objects introduced limitations on the robustness and scalabiility on the project.

The authors then discuss how can we actually make the implementation that takes the differences seriously.

##Limitations and my opinions
The paper is very well written and the ideas were well explained. It is true that we can not treat the objects as unified, but the solution that the authors propose--sort out and split the local and distributed object by an compiler--seems unrealistic to me. Furthermore, in the world of software-defined-networking, there would be an central controller that views everything in the network, and the distinction lied between partial failure and concurrency would disappear then. Moreover, with the advances in multi-core system and data center, the boundary between local and distributed system seems not obvious anymore.