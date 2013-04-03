---
layout: page
---

# Accessing the Source Code

The source code for Fénix Framework is available from
[this GitHub repository](https://github.com/fenix-framework/fenix-framework/).
We follow a [gitflow-oriented][gitflow] development process. To obtain the
version of the code that you want, you should take into account the following
branch names:

  * `master` ([here][master-branch]): Fénix Framework official releases
  * `develop` ([here][develop-branch]): Development branch.  Features are
    developed from this branch on forks of the main repository
  * `cloudtm/master` ([here][cloudtm-master-branch]): Fénix Framework releases
    customised for the [Cloud-TM project](http://www.cloudtm.eu/).
  * `cloudtm/develop` ([here][cloudtm-develop-branch]): Development branch for
    the Cloud-TM project's specific features.

During the second half of 2012, Fénix Framework has undergone a significant
refactoring, in which we added the concept of a *Backend*.  Each concrete
backend is a replaceable sub-module of the framework that provides the
bindings from the DML model to a concrete transaction system and a concrete
data storage system.  Above all, this allows the programmer to replace one
backend with another without having to rewrite any of the application code.
This is possible because the code generators provided by each backend maintain
a common API for the generated code.  This and many other core changes have
led to the release of Fénix Framework 2.0.

However, not all features from Fénix Framework 1.x we're initially ported to the
new architecture.  We intend to integrate all those features, but, until that
happens, the `master` branch will be dedicated to releases of versions 1.x, as
this is the version used, in production, by all applications that are deployed
using the Fénix Framework.  Versions 1.x will only support the JVSTM on top of
a MySQL database.  The already release version 2.0 supports only the backends
used in the Cloud-TM project, as porting all the 1.x codebase was more
complex.  That has been achieved and is already in the development branch,
ready to be included in release 2.1.  Hence, from release 2.1 upwards, the
framework fully supersedes the functionality of versions 1.x.

[master-branch]: http://www.github.com/fenix-framework/fenix-framework
[develop-branch]: http://www.github.com/fenix-framework/fenix-framework/tree/develop
[cloudtm-master-branch]: http://www.github.com/fenix-framework/fenix-framework/tree/cloudtm/master
[cloudtm-develop-branch]: http://www.github.com/fenix-framework/fenix-framework/tree/cloudtm/develop
[gitflow]: http://nvie.com/posts/a-successful-git-branching-model
