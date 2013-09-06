---
layout: page
---

# Pluggable Collections

The framework also allows parametrizing which collection is used internally in relations between Domain Objects. Three seperate modules can be found with regard to a B+Tree, a Linked List and a Skip List.

## The importance of collections

Pluggable collections are built on top of DML. This means that they are backend independent and automatically persisted. As a matter of fact, they can be used within the application for that purpose, although most uses should be relying on modelling with DML and relations between entities.

But besides that point, the actual importance of collections (namely, the abstraction of a set), is that it is used internally by the framework to model relations one-to-many between Domain Entities. Without going into further detail, this means that when a Person is declared in DML to have 0 or more Posts in a relation, this will automatically generate code that resorts to a DML, backend-independent collection to contain the Posts that the Person owns.

Therefore it makes sense to allow configuration of the collection to match different workloads. For instance, it can be that a very small relation takes more advantage of a Linked List, moreso if most of the accesses are insertions or removals. On the other hand, a very large relation with many queries can benefit more from a B+Tree. At the moment the configuration allows only to state a collection for the whole application, but this may be addressed in the near-future.

## Concurrency Friendliness

Finally, we have implemented additional optimizations in the Domain Collections with regard to concurrency friendliness in optimistic concurrency control. Briefly, these concurrency control protocols are implemented through the concepts of transactions that maintain keys read in a read-set, buffer the values written in a write-set, and eventually finish by attempting to commit. The transaction is successful if the read-set is valid, upon which event the writes are made available for new transactions in an atomic step. The idea here is to leverage on our knowledge of the specific data-structures that we use for the Domain Collections to perform optimizations that cause more transactions to be successful.

![Linked List example](images/linkedlist.png)

Let us consider the example presented in the figure above to guide our discussion. There, we can see a Domain Linked List with three values. For purposes of simplification, we use numbers here, whereas in practice (for instance) this would correspond to Infinispan keys that could be used to retrieve the actual Domain Objects held by this collection backing up some relation \textit{*-to-many}. Suppose that a transaction T_1 tries to insert element 9 and, concurrently, another transaction T_2 tries to remove element 20. In such case, both transactions follow the pointers of the list and bookkeep them on the read-set. Eventually, T_1 changes the next pointer of 7 (to insert 9) and T_2 changes the next pointer of 12 (to remove 20). In the event that T_1 commits first, this dooms the validation of the read-set of T_2, whose read-set entry for having read the next pointer of 7 became stale.

Yet, the key idea, which stems from the careful visualization of this access pattern, is that there is no apparent reason for why one transaction had to abort in this execution. In fact, T_2 would have executed the exact same action even if T_1 had never occurred. This leads us to the identification of structural, but semantically-irrelevant conflicts: these conflicts exist at the transactional data management level as it detected an invalid read-set; however, the high-level semantics of the data-structures, only known to the programmer (in this case, the FF developers), dictate that this conflict is benign and does not affect serializability. As a result, we created the concept of Ghost Reads, which are performed with the very same guarantees of normal, transactional reads, except that they are not added to the read-set of transactions. That means that such Ghost Reads can never cause aborts, but must be used carefully only when they cannot affect correctness.

We then exploited it within FF by carefully examining the data structures underlying each Domain Collection, and then changing them to use Ghost Reads in the right places. For sake of brevity, we will only hint on the key idea for the Ghost Linked List. For that, we resort once again to the figure. The idea of exploiting Ghost Reads here is for the Linked List implementation to traverse its contents only using Ghost Reads (instead of normal reads), thus avoiding making the transaction vulnerable to conflicts for elements in the list (that are semantically irrelevant for the method invoked). When the method reaches the desired point in the list, say because it wants to add an element, it requests the Infinispan to register the key read in the read-set: the one that defines the presence of the element, or its absence in the list).

This means that transaction T_1 traverses the first two next pointers with Ghost Reads, but then realises the second read defines the place where the new element should fit. Consequently it registers that read to make Infinispan bookkeep it in the read-set of T_1 (but not the previous reads). Concurrently, we can have T_2 seeking to remove element 20 further down the list in a similar fashion, without being subject to an abort due to the insertion of T_1. As a result, modifying the Linked-List now only implies in adding one element to the read-set, regardless of the size of the list.

The underlying reasoning for this process, still using the same example, is as follows: if some concurrent transaction T_3 removed element 20, then it is important that T_2 aborts when it validates at commit-time, because it also removed it---clearly not serializable. But in such case, T_2 is always sure to abort, even with the optimization of Ghost Reads, because the element it cares about is added to the read-set through the register invocation. The key idea is that removing element 20 commutes with any other concurrent access to the Linked-List, except insertion, removal, or search for 20.


## Co-location of collection's elements

In the distributed environments it is likely that data is only partially replicated to allow for larger scalability. In such event, it makes sense that the domain objects that compose a collection are all co-located. The exception is if such collection is simply too large not to fit within the memory of one machine. Therefore we leverage the support of Locality Hints to perform automatic optimizations with regard to data locality. Namely, we ensure that all properties of a Domain Object are co-located. This design decision stems from the fact that it is very likely that this set of properties, belonging to one object, are accessed under similar circumstances.

In addition to that, we also perform data co-location for the Domain Collections backing up the implementation of the relationships. For that, we also use the same group for all Domain Objects belonging to a Domain Collection instance, resulting in the co-location of the collection. Because this behaviour may not always be desirable (the collection may be too large to be co-located), we maintain different collections that are co-located and that are not. This means that we have both a Linked List and a Co-located Linked List.


## How to configure

There are two ways to configure:

1) Fine-grained parametrization over which collection is used in each relationship by specifying the "collection" attribute of the relation in the DML file. Example:


    relation DRWithBooks {
        .pt.ist.fenixframework.DomainRoot playsRole parent;
        Book playsRole theBooks {
            multiplicity *;
            indexed by id;
        }
    }

    relation DRWithAuthors {
        .pt.ist.fenixframework.DomainRoot playsRole parent;
        Author playsRole theAuthors {
            multiplicity *;
            indexed by id #(*);
            collection pt.ist.fenixframework.adt.bplustree.ColocatedBPlusTreeGhost;
        }
    }

    relation DRWithPublishers {
        .pt.ist.fenixframework.DomainRoot playsRole parent;
        Publisher playsRole thePublishers {
            multiplicity *;
            collection pt.ist.fenixframework.adt.linkedlist.LinkedList;
        }
    }


2) One-size-fits-all parametrization through the pom.xml as many other parameters. An example follows:


    <build>
        <plugins>
            <plugin>
                <groupId>pt.ist</groupId>
                <artifactId>dml-maven-plugin</artifactId>
                <version>2.0-SNAPSHOT</version>
                <configuration>
                    <codeGeneratorClassName>${fenixframework.code.generator}</codeGeneratorClassName>
                    <params>
                        <collectionClassName>pt.ist.fenixframework.core.adt.bplustree.BPlusTree</collectionClassName>
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



Note the single line within the 'configuration' tag:

    <collectionClassName>pt.ist.fenixframework.core.adt.bplustree.BPlusTree</collectionClassName>


### Possible configurations

Note that to use a specific collection, the dependency on the corresponding module must be estabilished in the pom.xml. This means that if one configures the collection to be the Linked-List, then it must add the dependency to the linked-list module.

The following collections are available with the corresponding modules:
* B+Tree - pt.ist.fenixframework.core.adt.bplustree.BPlusTree - adt-bplustree
* B+Tree improved in terms of performance - pt.ist.fenixframework.core.adt.bplustree.BPlusTreeArray - adt-bplustree
* Skip-List - pt.ist.fenixframework.core.adt.skiplist.SkipList - adt-skiplist
* Linked-List - pt.ist.fenixframework.core.adt.linkedlist.LinkedList - adt-linkedlist
* B+Tree with Ghost Reads - pt.ist.fenixframework.core.adt.bplustree.BPlusTreeGhost - adt-bplustree
* B+Tree improved in terms of performance and with Ghost Reads - pt.ist.fenixframework.core.adt.bplustree.BPlusTreeArrayGhost - adt-bplustree
* B+Tree with co-location - pt.ist.fenixframework.core.adt.bplustree.ColocatedBPlusTree - adt-bplustree
* B+Tree with co-location and with Ghost Reads - pt.ist.fenixframework.core.adt.bplustree.ColocatedBPlusTreeGhost - adt-bplustree

Each backend (and its respecive code generator) has a default collection which it uses if no configuration is given. It is the case that all transactional backends are using the B+Tree as the default collection.

