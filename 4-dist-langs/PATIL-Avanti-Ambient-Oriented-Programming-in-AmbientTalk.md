
<meta charset=utf8>

# "Ambient-Oriented Programming in AmbientTalk", Dedecke, Cutsem, Mostinckx, D’Hondt, and De Meuter July 2006

Avanti Patil <patil.av@husky.neu.edu>

## Synopsis
This paper presents a new age field in distributed programming called Ambient intelligence (AmI). With emerging IoT business new problems which can’t be solved by conventional methods in programming. This paper talks about AmbientTalk – which is Ambient-Oriented Programming language (AmOP). Properties of mobile networks prominently include volatile connections and open network. Implementing languages based on Ambient-Oriented Programming paradigm help to deal with such problems. Research in this paper show that needs of mobile network are different from stationary n/w., AmbientTalk which is based on AmOP paradigm helps to solve these problems.  These concepts were implemented in an ambient peer-to-peer instant messaging app which was deployed on smart phones as part of this research.

## Summary

* Motivation for Ambient-Oriented Programming language(AmOP)
- Connection volatility, Ambient resources, Autonomy in hardware, Natural Concurrency – these are collectively described as hardware phenomena which is inherent to mobile networks.  
- Previous distributed languages based on actor model and futures only solve problems for mobile networks partially.  
- Distributed Middlewares based on RPC, Data sharing, Publisher subscriber, Tuple based model do not fully solve problems of mobile networks like n/w volatility, ambient resources, autonomy and concurrency or well integrate with the object- oriented paradigm.
* Ambient-Oriented Programming 


## Analysis

Comparisons:
Languages like ABCL/f and Argus promote a scheme based on futures [14] partially solve problems related to mobile network but their objects block when accessing unresolved futures.
Janus, Salsa and E are the languages based on Actor models which use pure asynchronous communication. However, there is no support to discover ambient resources or handling of volatile connections among autonomous computing units. 

## References

