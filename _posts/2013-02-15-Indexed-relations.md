---
layout: post
# The author must exist on _config.yml
author: nmldiegues

# Here are some useful options:
# published: false
# title: Override here default title from file name
---

Also in 2.1-SNAPSHOT we have added a new feature called Indexed Relations. The
main idea is to allow efficient fetch of object(s) in a relation, by using one
of their attributes.

Putting it simple, if an Address has a country (attribute in the class), and a
Person is related to many Addresses, this allows easily obtaining the Address
of a Person that has a given country. Not only it is more efficient with regard
to single thread performance, but it is also transactional-friendly because it
reduces the footprint that makes a transaction vulnerable to aborts.

These indexes also allow multiple values to collide in the same indexed key. In
other words, a Person might have more than one Address with the same country. As
a result, the indexes would return a set of Addresses for a given query. This is
defined in the DML file according to the documentation. That allows the framework
to generate additional code in the Domain Classes to support the queries through
simple methods (once again, also described in the documentation).

Finally, the internal implementation is interestingly backend-independent by
relying on internal DML-based collections that back up the relations. As a matter
of fact, an index is implicitly stored in the collection that backs the relation.
Therefore using a unique index poses no additional overhead. This has resulted in
speedups of one order of magnitude across several benchmarks (synthetic and realistic)
and backends.
