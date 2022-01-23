# Storage classes

Each object in Amazon S3 has a storage class associated with it. There are different classes for

* frequently accessed objects (Standard)
* infrequently accessed objects (Standard-IA, one zone IA)
* objects with unknown or changing access patterns (Intelligent tiering)
* archived objects (Glacier).

## Pricing

To understand the need for different storage classes, let's first talk about how S3 is billed. You pay for

* the amount of data&#x20;
* number of requests
* retrieval fees for fetching data.&#x20;

For frequently accessed data you want to pay less for retrieval, but are prepared to pay more per GB of data.

With infrequently accessed data, you want the storage to be cheaper but are prepared to pay extra when you do need to retrieve it.&#x20;

Some data you just need to keep for 10 years due to some industry regulation and you may never need it. Here you want the storage to be dirt-cheap, the retrieval to be kind of cheap, but you are prepared to wait up to 12 hours to get the data if you need it.&#x20;

Take a look at the table here: [https://aws.amazon.com/s3/pricing/](https://aws.amazon.com/s3/pricing/)

You can also see how much using S3 would cost with different amounts of data with the AWS pricing calculator: [https://calculator.aws/#/](https://calculator.aws/#/)

## S3 Standard

The default storage class. If you don't specify the storage class when you upload an object, Amazon S3 uses the S3 Standard storage class.&#x20;

This is like "on-demand" storage. You can create and delete objects as you like and you only pay for what you use. You'll see that in other storage classes there may be a minimum retention time, where you have to pay for e.g. 30 days storage minimum. In standard there is no such time limit.&#x20;

#### Legacy: reduced redundancy&#x20;

There is an option called reduced redundancy. It used to be a very cheap storage class for data that is easily reproduced, that you don't mind losing and that you just want to store somewhere cheap. AWS recommend that you do not use this.&#x20;

## S3 Intelligent tiering

S3 Intelligent-Tiering is an Amazon S3 storage class designed to optimize storage costs by automatically moving data to the most cost-effective access tier, without performance impact or operational overhead.

## S3 Standard - IA&#x20;

IA stands for infrequently accessed. The S3 Standard-IA and S3 One Zone-IA storage classes are suitable for objects larger than 128 KB that you plan to store for at least 30 days. If an object is less than 128 KB, Amazon S3 charges you for 128 KB. If you delete an object before the end of the 30-day minimum storage duration period, you are charged for 30 days.

### S3 One Zone IA&#x20;

In Standard IA, the object data is stored redundantly across multiple AZ's. In One zone IA, the object data is only in one AZ. If the ZA is lost, so is your data.&#x20;

## Glacier

### **S3 Glacier Instant Retrieval**&#x20;

Use for archiving data that is rarely accessed and requires milliseconds retrieval. Data stored in the S3 Glacier Instant Retrieval storage class offers a cost savings compared to the S3 Standard-IA storage class, with the same latency and throughput performance as the S3 Standard-IA storage class. S3 Glacier Instant Retrieval has higher data access costs than S3 Standard-IA. For pricing information, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing/).

### **S3 Glacier Flexible Retrieval**&#x20;

Use for archives where portions of the data might need to be retrieved in minutes. Data stored in the S3 Glacier Flexible Retrieval storage class has a minimum storage duration period of 90 days and can be accessed in as little as 1-5 minutes using expedited retrieval. If you have deleted, overwritten, or transitioned to a different storage class an object before the 90-day minimum, you are charged for 90 days. For pricing information, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing/).

### **S3 Glacier Deep Archive**&#x20;

Use for archiving data that rarely needs to be accessed. Data stored in the S3 Glacier Deep Archive storage class has a minimum storage duration period of 180 days and a default retrieval time of 12 hours. If you have deleted, overwritten, or transitioned to a different storage class an object before the 180-day minimum, you are charged for 180 days. For pricing information, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing/).

S3 Glacier Deep Archive is the lowest cost storage option in AWS. Storage costs for S3 Glacier Deep Archive are less expensive than using the S3 Glacier Flexible Retrieval storage class. You can reduce S3 Glacier Deep Archive retrieval costs by using bulk retrieval, which returns data within 48 hours.

