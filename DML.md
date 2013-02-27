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

[cachopo-phd-dml]: https://github.com/fenix-framework/fenix-framework/blob/master-ff2/docs/dml-doc.pdf?raw=true
[cachopo-phd]: https://dspace.ist.utl.pt/bitstream/2295/132008/2/cachopo-phd.pdf
[dml-reference]: https://github.com/fenix-framework/fenix-framework/blob/master-ff2/docs/dml-reference.md

