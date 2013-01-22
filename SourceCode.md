---
layout: page
---

## Accessing the source code

The source code for Fénix Framework is available from
[this GitHub repository](https://github.com/fenix-framework/fenix-framework/).
To obtain the version of the code that you want, you should take into account
the following branches:

  * `master` ([here][master-branch]): Fénix Framework releases < v2.0
  * `master-ff2` ([here][master-ff2-branch]): Fénix Framework releases >= v2.0
  * `cloudtm` ([here][cloudtm-branch]): Fénix Framework releases customised
    for the [Cloud-TM project](http://www.cloudtm.eu/)

During the second half of 2012, Fénix Framework has undergone a significant
refactoring, in which we added the concept of a *Backend*.  Each concrete
backend is a replaceable sub-module of the framework that provides the
bindings from the DML model to a concrete transaction system and a concrete
data storage system.  Above all, this allows the programmer to replace one
backend with another without having to rewrite any of the application code.
This is possible because the code generators provided by each backend maintain
a common API for the generated code.  This and many other core changes have
led to the release of Fénix Framework 2.0.

However, not all features from Fénix Framework 1.x have yet been ported to the
new architecture.  We intend to integrate all those features, but, until that
happens, the `master` branch will be dedicated to releases of versions 1.x, as
this is the version used, in production, by all applications that are deployed
using the Fénix Framework.  Versions 1.x will only support the JVSTM on top of
a MySQL database.

New features will be developed and released to the `master-ff2` branch, until
it actually integrates all the features from 1.x.  When that happens the next
release will be integrated into `master` and `master-ff2` will no longer be
used.

[FF2]: http://www.github.com/fenix-framework/fenix-framework/tree/master-ff2

[master-branch]: http://www.github.com/fenix-framework/fenix-framework
[master-ff2-branch]: http://www.github.com/fenix-framework/fenix-framework/tree/master-ff2
[cloudtm-branch]: http://www.github.com/fenix-framework/fenix-framework/tree/cloudtm
