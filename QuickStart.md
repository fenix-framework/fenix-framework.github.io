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


    /* Here's a comment */
    public class A {
        public static void main (String [] args) { // e este codigo aqui pa dsoijfiosdj fiods fi
            System.out.println("ola");
        }
    }

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

