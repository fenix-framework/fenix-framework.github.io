---
layout: page
---


# Indexes Module

This module allows indexing domain objects in a relation efficiently. These are implemented using only components internal to the framework. Namely, it relies on internal backend-independent collections to maintain the indexes persistently and for fast querying using automatically generated methods.


## Usage

### Changes in the Domain Modeling Language

DML an additional attribute for each relation. This way, creating an indexed relation boils down to:

    class Person {}

    class Address {
        String country;
    }

    relation PersonHasAddresses {
        Person playsRole owner;
        Address playsRole address {
            multiplicity 0..*;
            indexed by country;
        }
    }


The attribute that instructs the framework to create the index is stated in bold. This means that each Person has at most one Address with a given country. If the index is deemed not to be unique, then the it must be declared in the following way: _indexed by country #(*);_


### Compilation Step

After enhancing the application's DML files with indexed relations you must recompile the application so that the newly generated domain classes (the *_Base.java ones) are upgraded with index search methods.


### Changing the application code

For each indexed relation, the previous step generates a new getter in the relation that receives as argument a possible value for the index, and returns the corresponding object(s) of the relation that have that value. The convention used is that the search by index queries are provided in methods:

    TypeOfDomainClass get<RelationName>By<SlotNameOfIndexedObject>(TypeOfSlotOfIndexedObject)

This means that the search methods receive an argument of the type of the attribute that we are using for the index. Moreover, the return is of the type of the domain class in the "-to-many" side of the relation. Whenever the index is not unique (stated by an arbitrary cardinality in the DML file), the result is instead a Set.

Using the example presented above, it would generate code as follows:

    public class Person_Base {
      (...)
      public Address getAddressByCountry(String country) {
        (...)
      }
    }

This allows an efficient search for a given Address. The typical usage is to refactor code similar to the following:

    Person john  = (...) // get John
    for (Address addr : john.getAddress()) {
        if (addr.getCountry().equals("Portugal")) {
            john.payTaxes(addr);
            return;
        }
    }

...into:

    Person john  = (...) // get John
    john.payTaxes(john.getAddressByCountry("Portugal"));

Not only the code becomes clearer, but also the implementation of the index is such that it performs better than the naive iterations/search.


## Design

The design of these indexes explores the DML based collections in the framework. These collections export a key-value API and are used to store the objects of relations "-to-many". For instance, the above example would have a collection holding John's addresses. To efficiently fetch an address with a given country, we use as keys in that relation the country of the addresses. This means that an indexed relation poses no overheads at all.

On the other hand, if the relation is indexed using an attribute that is not unique, then we batch the objects that collide in the index key. These batches use a collection, meaning that the collection of the relation actually holds several collections. This is decided statically upon compilation of the application, meaning that no runtime checks are performed, and the normal case of unique indexation is as efficient as we can get.

