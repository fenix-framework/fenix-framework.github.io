---
layout: page
---

# Quick start

To create a sample application pre-configured to work with the Fénix
Framework, execute the following Maven command and follow the interactive
mode:

    mvn archetype:generate -DarchetypeGroupId=pt.ist -DarchetypeArtifactId=fenix-framework-application-archetype-clean -DarchetypeVersion=2.0 -DarchetypeRepository=https://fenix-ashes.ist.utl.pt/maven-public

The parameters that you can give to the clean application archetype (using the
`-D` switch) are:

  * `fenixFrameworkVersion`:
    * Description: The version of the Fenix Framework to use
    * Default: Same as the archetype's version.
  * `backEndName`
    * Description: The name of the backend to use.  Some possible values are
      `mem`, `ispn` and `ogm`.
    * Default: `mem`
  * `backEndGroupId`
    * Description: The groupId of the backend module to use.
    * Default: `pt.ist` 
  * `backEndArtifactId`
    * Description: The artifactId of the backend module to use.
    * Default: `fenix-framework-backend-mem`
  * `backEndVersion`
    * Description: The version of the backend module to use.
    * Default: Same as the archetype's version. 
  * `codeGeneratorClassName`
    * Description: The code generator class provided by the chosen backend.
      Some possible values for each backend are:
        - `mem` -> `pt.ist.fenixframework.backend.mem.MemCodeGenerator`
        - `ogm` -> `pt.ist.fenixframework.backend.ogm.OgmCodeGenerator`
        - `ispn` -> `pt.ist.fenixframework.backend.infinispan.InfinispanCodeGenerator`
         
    * Default: `pt.ist.fenixframework.backend.mem.MemCodeGenerator`

## Hello world example

As a very simple example to illustrate the use of the Fénix Framework, let us
create a Java application with a minimalist domain model.

In this application we model one new entity: The `Person`.  We keep a
collection of `people` by establishing a relation between `Person` and
`DomainRoot`.  The `DomainRoot` is a built-in entity that represents a
possible root for the domain objects' model.  When the framework initializes,
it automatically creates one instance of the DomainRoot (if it doesn't already
exist). The Person type will have one instance for each person known.

Whenever the application is run we pass it the name of a new person and the
application adds that person to its set of known people and asks each one of
them to say "Hello".

Let us start by creating the basic application structure with:

    mvn archetype:generate -DarchetypeGroupId=pt.ist -DarchetypeArtifactId=fenix-framework-application-archetype-clean -DarchetypeVersion=2.0 -DarchetypeRepository=https://fenix-ashes.ist.utl.pt/maven-public -DgroupId=example -DartifactId=helloworld -Dversion=1.0-SNAPSHOT

Then, to define the domain model, edit the file in
`src/main/dml/helloworld.dml` and write the following content:

    package example;

    class Person {
        String name;
    }

    relation KnownPeople {
        .pt.ist.fenixframework.DomainRoot playsRole root;
        Person playsRole people { multiplicity *; }
    }

This defines the `Person` entity and adds a relation between `DomainRoot` and
`Person`, where a `DomainRoot` may have any number of `Person` related to it.
The package declaration is similar to the corresponding declaration in Java.

Now, this [DML][] code takes care of defining the structural aspects of the
domain model, but has no behavior in it. For instance, we would like that each
`Person` could say hello.  Also, in our example, we will be instantiating a
`Person` with a constructor that receives the name of the person and the
instance of the `DomainRoot`.

To implement the behavior, we write it in plain old Java. So, for the
`Person`, we may write (in Java, now):

    package example;

    import pt.ist.fenixframework.DomainRoot;

    public class Person extends Person_Base {
        
        public Person(String name, DomainRoot root) {
            setName(name);
            setRoot(root);
        }

        public void sayHello() {
            System.out.println("Hello, I'm " + getName() + ".");
        }
    }

The `Person` class extends the `Person_Base` class, which is an abstract class
generated by the Fénix Framework that implements the structural aspects of the
`Person` entity (likewise for any other entity defined in a DML file). 

To conclude this example, we just have to write our `HelloWorld` application,
which, as mentioned, will create the new people and then, ask all of them to
say hello.  This can be done with:

    package example;

    import pt.ist.fenixframework.Atomic;
    import pt.ist.fenixframework.DomainRoot;
    import pt.ist.fenixframework.FenixFramework;

    public class HelloWorld {

        // FenixFramework will try automatic initialization when first accessed
        public static void main(String [] args) {
            try {
                addNewPeople(args);
                greetAll();
            } finally {
                // ensure an orderly shutdown
                FenixFramework.shutdown();
            }
        }

        @Atomic
        private static void addNewPeople(String[] args) {
            DomainRoot root = FenixFramework.getDomainRoot();
            for (String name : args) {
                new Person(name, root);
            }
        }

        @Atomic
        private static void greetAll() {
            DomainRoot root = FenixFramework.getDomainRoot();
            for (Person p : root.getPeople()) {
                p.sayHello();
            }
        }
    }


Notice the use of the `@Atomic` annotation to demarcate the
[transactions][Transactions].  Operations involving domain objects must always
be performed inside a transaction.

And that's all.  Now, you just have to build it and run it.  The complete
example is available for download [here][helloworld-mem].  After decompressing
it, you can execute it with:

    mvn clean package exec:java -Dexec.mainClass="example.HelloWorld" -Dexec.args="John"

By default, the Fénix Framework is configured to use the in-memory
[backend][BackEnds], so, in the previous example, the application state is not
persisted between executions.  [Here][helloworld-ispn] is the same example,
now using the Infinispan backend to store the domain objects persistently.


[DML]: DML.html
[Transactions]: Transactions.html
[helloworld-mem]: examples/helloworld-mem.tgz
[helloworld-ispn]: examples/helloworld-ispn.tgz
[BackEnds]: BackEnds.html