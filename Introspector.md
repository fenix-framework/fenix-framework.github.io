---
layout: page
---

# TxIntrospector Module

The TxIntrospector module enables internal information gathering during a transaction. When enabled, the programmer may obtain a <tt>pt.ist.fenixframework.txintrospector.TxIntrospector</tt> instance, and use it to query what were the new/modified objects/relationships for a transaction.

## Enabling the Module

To activate this module, the DML Compiler needs to be invoked with the property <tt>ptIstTxIntrospectorEnable</tt> set to <tt>true</tt>. This can be achieved either (1) via the command line by adding the switch <tt>-param ptIstTxIntrospectorEnable=true</tt> (no spaces around <tt>=</tt>) to the invocation of the compiler, or (2) via the maven plugin by adding that same parameter to the code generation phase as shown in the following example:

    <build>
        <plugins>
            <plugin>
                <groupId>pt.ist</groupId>
                <artifactId>dml-maven-plugin</artifactId>
                <version>${project.version}</version>
                <configuration>
                    <codeGeneratorClassName>${fenixframework.code.generator}</codeGeneratorClassName>
                    <params>
                        <ptIstTxIntrospectorEnable>true</ptIstTxIntrospectorEnable>
                    </params>
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


## Accessing the TxIntrospector Instance

The FF client can obtain a TxIntrospector instance for a given transaction by calling <tt>TxIntrospector.getTxIntrospector(transaction)</tt>, or for the active transaction for the current thread via <tt>TxIntrospector.getTxIntrospector()</tt>.

While not enforced, it is recommended that the TxIntrospector be used only after the transaction commits, as information gathering is not thread-safe.

Note that calling <tt>TxIntrospector.getTxIntrospector()</tt> without activating the TxIntrospector module will result in a run-time exception.

Furthermore, although the TxIntrospector API may be used outside a transactional context, any access or operation on the domain objects contained therein should be performed inside an active transaction.


## TxIntrospector API

Please refer to the FF javadocs for the TxIntrospector class.


## Examples

Please refer to the test/test-txintrospector/ module for a sample test application that uses TxIntrospector.

