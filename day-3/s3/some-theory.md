# Some theory

## Encryption&#x20;

There are different ways to encrypt data in your S3 bucket:

* client-side encryption (that is, the file is already encrypted when it gets uploaded to S3)
* server-side encryption (SSE) with customer-managed keys (you own the keys)
* SSE with AWS-managed keys (KMS)
* SSE with S3-managed keys.

&#x20;We will talk more about KMS (Key management service) on day 5.&#x20;

## Access control

There are different ways of controlling who can access your buckets and objects:

* Bucket policies – Use IAM-based policy language to configure resource-based permissions for your S3 buckets and the objects in them.
* Access control lists (ACLs) – "Legacy". Grant read and write permissions for individual buckets and objects to authorized users. AWS recommends using S3 resource-based policies (bucket policies and access point policies) or IAM policies for access control instead of ACLs.&#x20;
* S3 Object Ownership – Disable ACLs and take ownership of every object in your bucket, simplifying access management for data stored in Amazon S3. You, as the bucket owner, automatically own and have full control over every object in your bucket, and access control for your data is based on policies. (This is what we used for our first bucket).

Read more about access control here: [https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-control-best-practices.html](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-control-best-practices.html)

## Replication&#x20;

How to put the same files in many buckets.&#x20;

One use case for replication is cross-**region** replication. All files uploaded to bucket A in some region get replicated automatically to bucket B in another region. This can be done to reduce latency (bring content closer to users) or to meet compliance requirements.&#x20;

There is also cross-**account** replication. Imagine a security team in a large corporation that needs to monitor logs from all the different AWS accounts: marketing, billing, development etc. Logs from different accounts can be consolidated in the security account.&#x20;

## Versioning

How to put many versions of the same file in the same bucket. This is the topic of our next lab.

