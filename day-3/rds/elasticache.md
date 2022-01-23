# ElastiCache

With DynamoDB, we had the option of using DAX or DynamoDB Accelerator as an in-memory cache in front of our database to reduce latency. For RDS, we have ElastiCache (ElastiCache can be used with other AWS services as well, but for the developer course it fits in best with RDS).&#x20;

Amazon ElastiCache makes it easy to set up, manage, and scale distributed in-memory cache environments in the AWS Cloud. It provides a high performance, resizable, and cost-effective in-memory cache, while removing complexity associated with deploying and managing a distributed cache environment.

## Redis and Memcached

Amazon ElastiCache supports the Memcached and Redis cache engines. Each engine provides some advantages.&#x20;

On the surface, the engines look similar. Each of them is an in-memory key-value store. However, in practice there are significant differences.

**Choose Memcached if the following apply for you:**

* You need the simplest model possible.
* You need to run large nodes with multiple cores or threads.
* You need the ability to scale out and in, adding and removing nodes as demand on your system increases and decreases.
* You need to cache objects.

**Choose Redis (clustered or not) if you need:**

* High availability
* Replication
* Geospatial indexing
* Backup and restore
* Online re-sharding
* Encryption
* Complex data types
* etc

Redis has many more features compared to Memcached.

\


