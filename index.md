---
layout: page
---

## Welcome to the Fénix Framework project

Fénix Framework allows the development of Java-based applications that need a
**transactional and persistent domain model**. Even though it was originally
created to support the development of web applications, it may be used to
develop any other kind of application that needs to keep a persistent set of
data.

One of the major design goals underlying the development of the Fénix
Framework is that it should provide a **natural programming model** to
programmers used to develop plain Java programs without
persistence. Unfortunately, the addition of persistence, typically backed up
by a relational database management system, interferes with the normal coding
patterns of object-oriented programming, because the need to access the
database efficiently precludes the use of many of the powerful mechanisms
available in the object-oriented paradigm.

Other frameworks and tools, often called Object/Relational Mappers, address
this problem by giving the programmer ways to specify how objects are mapped
into the relational database and by doing most of the heavy-lifting needed to
access the data in the database. Yet, they fail to hide completely the
presence of the database, meaning that the problem remains.

With the Fénix Framework, on the other hand, the database is completely hidden
from the programmer. This has two consequences: (1) the programmer cannot
control the mapping of objects to the database, and (2) there is no way to
take advantage of database facilities such as joins or aggregate functions. In
return, programmers may use, and are encouraged to do it, all of the normal
object-oriented programming coding patterns.

Programmers using the Fénix Framework specify the structure of the
application's domain model using a new domain-specific language created to
that effect, the Domain Modeling Language (DML), and then develop the rest of
the application in plain Java.

## Status

The core code of what later became the Fénix Framework started to be developed
in 2004 and was first put into production in 2005, as part of the
[FénixEDU][FénixEDU] project. It has been powering the FénixEDU web
application since then, and several other applications over the last couple of
years. It is, therefore, a mature framework, even though it continues to be
developed and there are many lines of development pushing it into other
directions.

[FénixEDU]: https://fenix-ashes.ist.utl.pt/fenixWiki
