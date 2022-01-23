---
description: Serverless, schemaless NoSQL database
---

# DynamoDB

## What is DynamoDB?

In AWS' own words,

"Amazon DynamoDB is a fully managed NoSQL database service that provides fast and predictable performance with seamless scalability. DynamoDB lets you offload the administrative burdens of operating and scaling a distributed database so that you don't have to worry about hardware provisioning, setup and configuration, replication, software patching, or cluster scaling."

DynamoDB is the database of choice for most serverless architectures in AWS. It is a schemaless (non-relational) database that consists of key-value pairs.

## DynamoDB core components

Before we are able to get started with our first lab, we have to introduce some core components of DynamoDB such as

* tables
* items
* attributes
* partition keys
* sort keys

### Tables, items and attributes

In DynamoDB, tables, items, and attributes are the core components that you work with. A _table_ is a collection of _items_, and each item is a collection of _attributes_.

#### **Tables**&#x20;

Similar to other database systems, DynamoDB stores data in tables. A _table_ is a collection of data.

#### Items&#x20;

Each table contains zero or more items. An _item_ is a group of attributes that is uniquely identifiable among all of the other items.&#x20;

#### **Attributes**

Each item is composed of one or more attributes. An _attribute_ is a fundamental data element, something that does not need to be broken down any further.

![Example of a People table with three items. PersonID is an attribute.](<../../.gitbook/assets/image (141).png>)

### Partition keys and sort keys

When you create a table, in addition to the table name, you must specify the primary key of the table. The primary key uniquely identifies each item in the table, so that no two items can have the same key.

DynamoDB supports two different kinds of primary keys:&#x20;

#### Partition key

A simple primary key, composed of one attribute known as the _partition key_. (E.g. PersonID)

#### **Partition key and sort key**&#x20;

Referred to as a _composite primary key_, this type of key is composed of two attributes. The first attribute is the _partition key_, and the second attribute is the _sort key_.

Now we have the information necessary to create our first table.&#x20;
