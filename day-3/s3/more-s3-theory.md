# More S3 theory

## Multi-part upload

The largest object that can be uploaded with a single PUT request is 5 GB. Multi-part uploads are mandatory for objects larger than 100 GB.

## Lifecycle rules

You can set rules that move objects from one storage class to another after a time period. Suppose you need last month's billing data regularly, but once the data is over 30 days old it is read very infrequently if at all.&#x20;

You could keep the data you need to read frequently in Standard and after 30 days move it to Standard-IA. Maybe some regulation says that the data needs to be archived for 2 years: after 3 months in Standard-IA the data could be moved to Glacier.

## Data consistency

Amazon S3 delivers strong read-after-write consistency automatically, without changes to performance or availability, without sacrificing regional isolation for applications, and at no additional cost.

After a successful write of a new object or an overwrite of an existing object, any subsequent read request immediately receives the latest version of the object. S3 also provides strong consistency for list operations, so after a write, you can immediately perform a listing of the objects in a bucket with any changes reflected.

## Misc

Object lock: you can for example disable editing for audit logs

requester pays: serve content so that whoever requests it pays&#x20;

transfer acceleration: speed up uploads to S3 using CloudFront edge locations

the list goes on..&#x20;

For all the details see [https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html)

##
