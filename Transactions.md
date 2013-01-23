---
layout: page
---

# Transaction Support

The Fénix Framework provides transaction support to applications through three
distinct mechanisms:

  * standard *begin*, *commit* and *rollback* operations common to any
    transactional system;
  * Atomic invocation of a *Callable*;
  * Atomic invocation of methods that use the `@Atomic` annotation.

## Low-level mechanism for transaction management

The `pt.ist.fenixframework.TransactionManager` interface provides the
low-level API for managing a transaction. Using this low-level API the
programmer has a fine-grained control over the transaction, but at the expense
of added complexity.

The framework's `TransactionManager` extends the
`javax.transaction.TransactionManager` interface, which means that any
application already using the Java Transactions API can be easily ported to
use the Fénix Framework.

Starting a new transaction requires the invocation of the `begin()` method.
The transaction created will be active, within the current thread, until
either a commit or a rollback occurs.

When the programmer wants to finish the transaction, making any changes
available to others he must invoke the `commit()` method on the
`TransactionManager`. This method will commit any changes performed on the
active transaction, within the current thread, and close the transaction.

If the programmer sees fit to abnormally terminate the current transaction, he
can do so through the `rollback()` method. This method will close the
transaction, discarding any changes that may exist.

Here's an example code:

    import pt.ist.fenixframework.FenixFramework;
    import pt.ist.fenixframework.TransactionManager;

    public class SomeClass {
        // ...

        void someMethod() {
            // non-transactional code
            
            TransactionManaer tm = FenixFramework.getTransactionManager();
            tm.begin();
            boolean txCommited = false;
            try {
                // transactional code
                tm.commit();
                txCommited = true;
            } finally {
                if (! txCommited) {
                    tm.rollback();
                }
            }

            // non-transactional code
        }

        // ...
    }

## Transactional commands

Another way to provide transaction support is to program the behaviour that
needs to be transactional in the call() method of a
`java.util.concurrent.Callable`.

    import java.util.concurrent.Callable;
    import pt.ist.fenixframework.TransactionManager;

    public class SomeClass {
        // ...

        void someMethod() {
            // non-transactional code

            FenixFramework.getTransactionManager().withTransaction(new Callable() {
                    @Override
                    public Object call() {
                        // transactional code
                    }
                });

            // non-transactional code
        }

        // ...
    }

## @Atomic annotation

The easiest way to execute a method in its own transaction is to simply
annotate it with the `@Atomic` annotation. During the post-compile phase the
annotated methods are surrounded by transaction management code using the
lower-level APIs.

    import pt.ist.fenixframework.Atomic;
    
    public class SomeClass {
        //...

        @Atomic
        void atomicMethod() {
            // transactional code
        }

        // ...
    }

