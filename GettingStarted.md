---
layout: page
---

# Getting Started

The Fénix Framework is useful for applications that need to have a
[transactional][Transactions] and persistent [domain model][DML].

Typically, a domain model is programmed in Java by using Java classes to
implement the domain model's entities.  Relationships between entities,
however, do not map that easily into Java constructs.  Instead, they are
typically implemented in each of the participating entities' classes, either
as references to other objects, or as collections of objects, or both.

Moreover, classes corresponding to domain model's entities have other
requirements that are not common to classes implementing other types of
objects in the application.  For instance, their objects need to be persistent
and are typically shared among many concurrent threads.  So, these classes
need to be implemented specially, taking these requirements in consideration.

Given the special nature and needs of the domain model, in the Fénix Framework
the domain model is defined using a new language that was specifically created
to allow the definition of the structural aspects of a domain model. This
language is the [DML][] (Domain Modeling Language). A domain model defined in
the DML is then compiled into the corresponding Java classes that correctly
implement that domain model structure in such a way that allows the
programmers to further define the entities' behaviors in plain Java.

The Fénix Framework stores the application's entities in a storage that
depends on the selected [Backend][BackEnds].  It does that automatically and
transparently to the programmer.

So, in a nutshell, to use the Fénix Framework programmers have to do the
following:

  1. Define the structure of the domain model in DML ([read more][DML]);
  2. Write the rest of the program in plain old Java;
  3. Use the `@Atomic` annotation on the methods that should execute
  atomically ([read more][Transactions]);
  4. Somewhere in the Java code, initialize the framework. Since version 2.0,
  the framework attempts automatic initialization when first accessed
  ([read more][javadoc-ff-init]);
  5. Configure your storage.  This will be used to store the application's
  data ([read more][BackEnds]);
  6. Build and run the application.

Have a look at the [Quick Start page][QuickStart] for a guide to create your
first Fénix Framework-based application.


[Transactions]: Transactions.html
[DML]: DML.html
[BackEnds]: BackEnds.html
[javadoc-ff-init]: apidocs/2.0/pt/ist/fenixframework/FenixFramework.html
[QuickStart]: QuickStart.html
