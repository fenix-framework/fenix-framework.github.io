---
layout: page
---

# Pluggable Collections

The framework also allows parametrizing which collection is used internally in relations between Domain Objects. Three seperate modules can be found with regard to a B+Tree, a Linked List and a Skip List.


## Programmer-Defined Caching 

We have identified a possible source of optimizations governed by the programmer's will. We recall that sometimes there are domain objects that can be accessed with some staleness without threatening correctness, as is the case of Ghost Reads. However, Ghost Reads still impose a remote operation to fetch the data item in the event that it is not replicated in the local machine. Therefore we introduced an additional facility to capture this use case, given by the name of L2 Cache, as opposed to the L1 Caching mechanism available in Infinispan. The key difference is that the L1 Cache is transactional, whereas L2 delegates any guarantees to the programmer who uses it. Therefore it should be seen as another facility to maximize performance for expert users.

To accommodate the L2 Cache in the framework we generate additional methods in the compilation phase of the DML. Every getter method now has a respective cachedGetter that first checks the L2 Cache for the object, and only in its absence shall it request Infinispan to process the request. When such method is queried, and the cache does not contain the object, the result returned by Infinispan is placed in the cache. This cache is a bounded concurrent hash map, back-ported from Java JDK8 version (which is still under development, but has shown considerable gains over previous versions). The method referred is also parametrized to allow the programmer to force a miss in the cache and freshen it.

One envisioned use case for the Cache is for the programmer to fetch an object using the cached method, thus skipping any remote access if there is a hit. In the event that the returned result does not match the expectations of the programmer, which is governed by logic specific to the application and the programmer's knowledge, he can then repeat the operation by forcing a miss and incurring in the full-blown transactional request through Infinispan. The advantage of this is that the first operation incurs in negligible overheads, and the second operation should occur seldom. Moreover, we note that normal operation of users that do not explore this facility incurs in no overheads as the L2 Cache is only used when the cached methods are used.


## Example

    relation DomainRootHasRoot {
        .pt.ist.fenixframework.DomainRoot playsRole;
        Root playsRole app;
    }


    relation RootHasAgents {
        Root playsRole; //For unidirectional write no role name
        Agent playsRole agents {

            multiplicity *;
            indexed by user;
        }
    }

and

    DomainRoot domainRoot = FenixFramework.getDomainRoot(); // this is only local, always
    Root appRoot = domainRoot.getApp(); // this touches ISPN, might be remote
    appRoot = domainRoot.getAppCached(false); // this uses the L2 cache
    appRoot = domainRoot.getAppCached(true); // forces a cache miss and update

and 

    Set<Agent> agents = appRoot.getAgents(); // gets a wrapper to the domain collection, might be remote
    agents = appRoot.getAgentsCached(false); // uses the L2 cache to get the domain (just the "start" point of the collection, not all of it of course --- getting the rest is a lazy process as required by iterators/getters)
    for (Agent agent : agents) // iterates the collection using implicitly "getters with cached(false)"; if the collection had been fetched normally (as in the first line), the iteration would also be normal; there is no easy way here to iterate with "getters cached(true)", because the iteration is implicit and some behavior had to be defined as the default one, so the get cached false seemed to make more sense;

and 

    Agent agent = appRoot.getAgentByUser("user1"); // might be remote
    appRoot.getAgentByUserCached(false, "user1");  // uses the L2 cache
    appRoot.getAgentByUserCached(true, "user1");  // forces a cache miss and update

the above also works for multi-value index (i.e., non-unique, by using the DML indexed by xxx #(*))


## Usage

The L2 Cache does not affect the normal execution, i.e., those where cached methods are not invoked. Therefore there is no knob to enable to use the L2 Cache; it is merely left for the programmer to invoke the cached methods.

