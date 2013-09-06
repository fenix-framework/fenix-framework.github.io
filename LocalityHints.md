---
layout: page
---

## Overview

When using Cloud-TM's platform, the application data is ultimately stored in a data grid that supports a key-value structure.  Part of the information that can be represented in the keys is used by the platform for data grouping and automatic data placement.  However, the programming model provided by Cloud-TM focuses on object-orientation: Programmers use concepts such as classes, attributes and relations, which are then translated to data that is stored in a key-value format.

The Locality Hints feature in Fénix Framework provides the abstraction that allows programmers to supply locality-related information about their data, so that the platform can internally use to group it and distributed it across the nodes.

Because, locality hints end up as part of the keys stored in the data grid, they need to be provided at object creation and cannot be changed afterwards.  To that effect, DomainObjects have a constructor that takes LocalityHints as parameter.  Each DomainObject instance has a method for obtaining its own LocalityHints, which were provided at creation time (DomainObject.getLocalityHints() and DomainObject.getLocalityHints()).

The LocalityHints can be used to store an arbitrary value for the Grouping API and any data placement Features.  All the information in a locality hint is stored as part of the object's related keys in Infinispan.  An instance of LocalityHints holds information composed of pairs in the form of feature-name/feature-value.

Feature names can be any string.  However, there is a reserved name for providing the value to use in the GROUP_ID. Any other name is considered an auto-placer feature name.

The Fénix Framework provides a method to obtain an object's 'storage keys' (DomainObject.getStorageKeys()). The programmer can give these keys to the Distributed Execution Framework to 'hint' the best location to execute a command.

