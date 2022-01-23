---
description: Some useful concepts to glance at.
---

# Theory

## TTL

With a Time to live or TTL attribute you can have old items in your table automatically expire after a certain time. This is useful for data that is short-lived. It is straightforward to implement, so go ahead and try it in the console if you like.&#x20;

## DynamoDB Accelerator

Amazon DynamoDB is designed for scale and performance. In most cases, the DynamoDB response times can be measured in single-digit milliseconds. However, there are certain use cases that require response times in microseconds. For these use cases, DynamoDB Accelerator (DAX) delivers fast response times for accessing eventually consistent data.

DAX is a DynamoDB-compatible caching service that enables you to benefit from fast in-memory performance for demanding applications. Basically an in-memory cache that sits in front of your database to speed up reads.&#x20;

## DynamoDB encryption&#x20;

All user data stored in Amazon DynamoDB is fully encrypted at rest. DynamoDB encryption at rest provides an additional layer of data protection by securing your data in an encrypted table—including its primary key, local and global secondary indexes, streams, global tables, backups, and DynamoDB Accelerator (DAX) clusters whenever the data is stored in durable media.

You can learn more about DynamoDB in AWS's documentation:

{% embed url="https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html" %}
AWS documentation
{% endembed %}
