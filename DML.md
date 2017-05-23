---
layout: page
---

# Domain Modelling Language (DML)

The Domain Modeling Language (DML) is a micro-language designed specifically
to implement the structure of a domain model. It has constructs for specifying
both entity types and associations between entity types.

DML has a Java-like syntax to be easy to learn by Java programmers.

This page will document the language, but until then, you may find basic
documentation in [this excerpt][cachopo-phd-dml] of the
[PhD thesis that introduced the DML][cachopo-phd].  Also, more advanced (and
newly added) aspects of the language are available in
[this doc][dml-reference].

Please note that the syntax for the association declaration has changed with
regard to the text in the thesis: The keyword `relation` is used instead of
the keyword `association`.

## Built-in value types in the Fénix Framework

The built-in value types (meaning that you can use them without declaring
them) are the following:

  * The Java primitive types: `boolean`, `byte`, `char`, `short`, `int`,
    `float`, `long`, and `double`.
  * The wrapper types for primitive Java types: `Boolean`, `Byte`,
    `Character`, `Short`, `Integer`, `Float`, `Long`, and `Double`. These
    types are typically used when an entity's field is optional (because it
    may be `null` in this case).
  * The `String` type.
  * The `Serializable` type.
  * The `bytearray` type (it will map into the Java type `byte[]`).
  * To represent dates and related types, there are the following built-in
    value-types, from the [JodaTime](http://joda-time.sourceforge.net/) API:
    `DateTime`, `LocalDate`, `LocalTime`, and `Partial`.
  * For greater flexibility, the Framework support's [GSON's](https://code.google.com/p/google-gson/) 
    `JsonElement` to allow the representation of unstructured data.

## Example

Here are a few examples of the use of DML to declare two domain classes of a hypothetical banking application:

    class Client {
        String name;
    }

    class Account {
        int balance;
        boolean closed;
    }

A relation between these two classes would look like this:

    relation ClientAccounts {
        Client playsRole client;
        Account playsRole accounts {
            multiplicity *;
        }
    }

The framework's code generator processes DML files with these classes and relations.
It creates a Java class for each DML class, with getter and setter methods for each slot, and each relation between classes.
The generated methods use the names after the "playsRole" keyword.
Here's an example of the use of the generated methods for the Client and the Account:

    public class Client extends Client_Base {
        // (...)
        public int getTotalBalance() {
            int totalBalance = 0;
            for (Account account : getAccounts()) {
                totalBalance += account.getBalance();
            }
            return totalBalance;
        }
    }



[cachopo-phd-dml]: https://github.com/fenix-framework/fenix-framework/blob/master/docs/dml-doc.pdf?raw=true
[cachopo-phd]: https://drive.google.com/file/d/0B8hMVUjuYxUKYU0xRzAzZWIxVWs/view?usp=sharing
[dml-reference]: https://github.com/fenix-framework/fenix-framework/blob/master/docs/dml-reference.md

