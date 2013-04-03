---
layout: page
---

# BackEnds

Regardless of which backend the programmer uses, the Fénix Framework provides
automatic management of bidirectional relations between domain entities
modelled in DML (for example, when adding an object to a collection, both ends
are updated).

Bundled with Fénix Framework 2.0, there are currently two backend
implementations: The *Infinispan Direct backend*, and the *OGM backend*.  Both
backends provide bindings to the underlying storage provided by
[Infinispan's][Infinispan] cache.  However, each backend provides a different
mapping between the objects and their key-value representation.

## Infinispan Direct backend

The Infinispan Direct backend leverages on the fact that Infinispan's cache is
already build into the application's memory space, and thus uses very
lightweight domain objects.  These objects' attributes and relations to other
objects don't take up any additional memory space.  Whenever the value of any
attribute or relation is requested, the generated code fetches the value
directly from the corresponding cache entry in Infinispan.  As such, the
objects themselves, are *hollow*, in the sense that they only contain their
primary key attribute.

## OGM Backend

The OGM backend relies on [Hibernate Core's][hibernate-core] engine, stacked
with the [Hibernate Object/Grid Mapper][hibernate-ogm] to do the actual
load/store operations from/to Infinispan.  As such, the code generation for
domain entities creates typical *POJO*s together with a standard Java
Persistence API mapping.

## STM-based backends

The first implementations of the Fénix Framework (prior to version 2.0) have
no notion of replaceable backend, and support only an STM-based transactional
system (using [JVSTM][JVSTM]).  The [main development branch][ff-master]
supported only data storage on top of [MySQL][mysql] using [OJB][OJB] as data
mapper.  There was, however, parallel work that supported some NoSQL storage
systems ([HBase][], [Berkeley DB][BDB], [Infinispan][]) to a limited extent.
Namely, on top of NoSQL databases, Fénix Framework only supports a single
node, whereas on top of MySQL it can run multiple application servers.

We are currently working to develop and STM-based backend on the new
architecture.  We plan to provide storage support for at least MySQL and
Infinispan.

[mysql]: http://www.mysql.com/
[OJB]: http://db.apache.org/ojb/
[ff-master]: http://github.com/fenix-framework/fenix-framework/
[JVSTM]: http://jvstm.github.com
[Infinispan]: http://www.jboss.org/infinispan
[hibernate-core]: http://www.hibernate.org/
[hibernate-ogm]: http://www.hibernate.org/subprojects/ogm.html
[HBase]: http://hbase.apache.org/
[BDB]: http://www.oracle.com/technetwork/products/berkeleydb/overview/index.html
