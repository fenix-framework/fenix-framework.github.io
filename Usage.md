---
layout: page
---

# How to use Fénix Framework
<!-- to build and run your application -->

## Introduction

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
first Fénix Framework-based application.  In the following we describe how to
integrate the Fénix Framework in your application development cycle.

[Usage]: Usage.html
[Transactions]: Transactions.html
[DML]: DML.html
[BackEnds]: BackEnds.html
[javadoc-ff-init]: apidocs/2.0/pt/ist/fenixframework/FenixFramework.html
[QuickStart]: QuickStart.html

## Build and run your application

The Fénix Framework is provided as a set of JAR files, which should be made
available to the application both during compile and runtime.  At compile
time, the Fénix Framework is required to generate the backend-specific code of
the domain entities.  At runtime it is used both by the generated code and by
the application code.  Here we describe the general procedure to setup the
Fénix Framework to develop an application.

The framework is developed using [Maven][], so if you use Maven to build your
application, you can just depend on the framework's artifacts that you need,
by adding them to your `pom.xml`:

    <dependencies>
        <!-- add dependencies for the desired backends -->
        <dependency>
            <groupId>pt.ist</groupId>
            <artifactId>fenix-framework-backend-infinispan</artifactId>
            <version>2.0-cloudtm</version>
        </dependency>
    </dependencies>

These artifacts are available via the
[Fénix Framework Nexus repository][fenix-maven-repository], so you need to add
it to your configuration:

    <pluginRepositories>
        <pluginRepository>
            <id>fenix-ashes-maven-repository</id>
            <url>https://fenix-ashes.ist.utl.pt/maven-public</url>
        </pluginRepository>
    </pluginRepositories>

    <pluginRepositories>
        <repository>
            <id>fenix-ashes-maven-repository</id>
            <url>https://fenix-ashes.ist.utl.pt/maven-public</url>
        </repository>
    </repositories>

Additionally, you will probably want to hook the `dml-maven-plugin` to your
build process, so that your domain classes get properly generated and
post-processed.  This can be achieved by adding the plugin to the build phase.

    <build>
        <plugins>
            <plugin>
                <groupId>pt.ist</groupId>
                <artifactId>dml-maven-plugin</artifactId>
                <version>${fenixframework.version}</version>
                <configuration>
                    <codeGeneratorClassName>${fenixframework.code.generator}</codeGeneratorClassName>
                </configuration>
                <executions>
                    <execution>
                        <goals>
                            <goal>generate-domain</goal>
                            <goal>post-compile</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>

The `fenixframework.code.generator` property, shown in the previous listing,
could be set in your properties section of the POM file, as per the following
example:

    <properties>
        <fenixframework.code.generator>
            pt.ist.fenixframework.backend.infinispan.InfinispanCodeGenerator
        </fenixframework.code.generator>
        <!-- alternative value could be pt.ist.fenixframework.backend.ogm.OgmCodeGenerator -->
    </properties>

Just make sure that `fenixframework.version` property is set in accordance
with the version used for the other Fénix Framework modules.  This ensures
that you use a plugin that matches the version of the framework you are using.

The steps described above are all that is necessary to be able to develop
using the Fénix Framework.  The Maven build system will automatically download
the required artifacts and the plugins will hook to the correct phases of your
build.

Additionally, you may opt to compile the framework from source.  It can be
downloaded from GitHub and packaged with:

    git clone git://github.com/fenix-framework/fenix-framework.git
    cd fenix-framework
    mvn package

The previous sequence produces the artifacts, i.e. one JAR file for each Fénix
Framework sub-module.  To use the Fénix Framework in your application, these
packaged JAR files are required.  They can be installed to the local Maven
repository with:

    mvn install

If you use any system other than Maven, first make sure that the jars
resulting from the execution of `mvn package` are visible in your
application's build and runtime classpaths, and then check for any additional
dependencies.  You can check for dependencies using the
[Maven dependency plugin](http://maven.apache.org/plugins/maven-dependency-plugin/)
(even if you don't use Maven in your application):

    mvn dependency:list

At this point your application should be set up correctly to integrate the
Fénix Framework in its build dependencies.  **If you are not using Maven** to
build you application, then you need to take care of two additional steps.
The first is to run the DML Compiler (Java program
`pt.ist.fenixframework.DmlCompiler`) to generate the source base classes
before compiling your own code, and the second is to run the post-processor
(Java program `pt.ist.fenixframework.core.FullPostProcessDomainClasses`) on
your compiled classes.  Both tools come available with the Fénix Framework
code in the `fenix-framework-dml-compiler` sub-module.

Finally, when deploying your application ensure that Fénix Framework and all
of its dependencies are available in the `CLASSPATH`.

[Maven]: http://maven.apache.org/
[fenix-maven-repository]: https://fenix-ashes.ist.utl.pt/nexus/
