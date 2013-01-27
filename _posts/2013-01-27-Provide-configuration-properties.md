---
layout: post
---

## How to provide Fénix framework configuration properties

In 2.1-SNAPSHOT we've updated how configuration by convention works.  It is
now possible to give configuration properties in three ways:

  1. using file `fenix-framework.properties`;

  2. using file `fenix-framework-NNN.properties`, where `NNN` is the name of
  the BackEnd of the generated domain-specific code;

  3. system properties (the `-D` switch), with the `fenixframework.` prefix
  added (e.g. `-Dfenixframework.appName` to set property `appName`).

Priority increases from the first to the last item listed.  These three
mechanisms can be combined.  In case of setting the same property more than
once, the setting with the most priority wins.


