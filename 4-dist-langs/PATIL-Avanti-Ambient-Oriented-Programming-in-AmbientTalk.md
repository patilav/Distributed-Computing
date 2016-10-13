
<meta charset=utf8>

# "Ambient-Oriented Programming in AmbientTalk", Dedecke, Cutsem, Mostinckx, D’Hondt, and De Meuter July 2006

Avanti Patil <patil.av@husky.neu.edu>

## Synopsis
This paper presents a new age field in distributed programming called Ambient intelligence (AmI). With emerging IoT business new problems which can’t be solved by conventional methods in programming. This paper talks about AmbientTalk – which is Ambient-Oriented Programming language (AmOP). Properties of mobile networks prominently include volatile connections and open network. Implementing languages based on Ambient-Oriented Programming paradigm help to deal with such problems. Research in this paper show that needs of mobile network are different from stationary n/w., AmbientTalk which is based on AmOP paradigm helps to solve these problems.  These concepts were implemented in an ambient peer-to-peer instant messaging app which was deployed on smart phones as part of this research.

## Summary

AmbientTalk features a classless double-layered object model. Ac- tors are visible in mobile networks and communicate with each other in a non- blocking way. Internally, they contain a graph of passive objects. Actors have four mailboxes which reify their communication traces and four mailboxes which are causally connected to the outside world to reflect the evolution of acquaintances in the ambient. These properties turn AmbientTalk into an AmOP language 

* Motivation for Ambient-Oriented Programming language(AmOP)

(1) Connection volatility, Ambient resources, Autonomy in hardware, Natural Concurrency – these are collectively described as hardware phenomena which is inherent to mobile networks. 

(2) Previous distributed languages based on actor model and futures only solve problems for mobile networks partially.

(3) Distributed Middlewares based on RPC, Data sharing, Publisher subscriber or Tuple based model do not fully solve problems of mobile networks like n/w volatility, ambient resources, autonomy and concurrency or well integrate with the object- oriented paradigm.

* Ambient-Oriented Programming 

(1)	Classless Object Models 

(2)	Non-Blocking Communication Primitives 

(3)	Reified Communication Traces 

(4)	Ambient Acquaintance Management 

* The AmbientTalk Kernel 

(1) A Double-layered Object Model 

(2) The Passive Object Layer 

(3) The Active Object Layer 

(4) First-class Mailboxes 

* Example: AmbientTalk at Work: AmbientChat 

(1) Ambient References 

(2) Non-blocking Futures 

(3) Due: Handling Failures 

(4) Due-blocks 

* Conclusion and Future Work 

## Analysis

Comparisons:
Languages like ABCL/f and Argus promote a scheme based on futures [14] partially solve problems related to mobile network but their objects block when accessing unresolved futures.
Janus, Salsa and E are the languages based on Actor models which use pure asynchronous communication. However, there is no support to discover ambient resources or handling of volatile connections among autonomous computing units. 

## References


