---
layout: page
---

# Programmer-defined Transactional Classes

This is a custom API in Fénix Framework to allow the programmer to define the transactional class that is executing in the context of the current transaction. This transactional context metadata can be helpful for other features/modules of the platform, such as load balancing requests.


## Usage

This API is very similar to the normal transaction construct --- it merely adds an additional parameter as the following example shows; that is, the transactional class identifier, which is of type String:

    public <T> T withTransaction(Callable<T> command, String transactionalClassId)

and

    FenixFramework.getTransactionManager().withTransaction(new Callable<Object>() {
        @Override
        public Object call() throws Exception {
            // application logic goes here
        }
    , "Transaction123");


Note the transactional class identifier "Transaction123" in the example.

