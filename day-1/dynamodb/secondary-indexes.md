# Secondary indexes

From AWS documentation:

"A _secondary index_ is a data structure that contains a subset of attributes from a table, along with an alternate key to support `Query` operations. You can retrieve data from the index using a `Query`, in much the same way as you use `Query` with a table. A table can have multiple secondary indexes, which give your applications access to many different query patterns."

DynamoDB supports two types of secondary indexes: global and local.&#x20;

#### Global secondary index (GSI)

An index with a partition key and a sort key that can be different from those on the base table.

#### Local secondary index (LSI)

An index that has the same partition key as the base table, but a different sort key.
