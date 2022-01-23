# 🪣 S3

The sales pitch:

"Amazon Simple Storage Service (Amazon S3) is an object storage service that offers industry-leading scalability, data availability, security, and performance."

So what is it really? Object storage. Sort of like Google Drive or Dropbox, you can store files in S3. It is object storage, so you can't run an operating system there (unlike EBS which is block storage), but you can upload just about any kind of media into S3.&#x20;

## S3 Concepts

### Object

Objects are the fundamental entities stored in Amazon S3. Objects consist of object data and metadata. The metadata is a set of name-value pairs that describe the object.

In plain English, think of objects like "files".&#x20;

### Bucket

A bucket is a container for objects stored in Amazon S3. Kind of like a folder in Windows is a container for the files in the folder.&#x20;

Buckets are created in a certain AWS region, but the name of the bucket has to be globally unique (because it has to be a valid DNS name).&#x20;

### Key

An _object key_ (or _key name_) is the unique identifier for an object within a bucket. Every object in a bucket has exactly one key.&#x20;

Every object in Amazon S3 can be uniquely addressed through the combination of the web service endpoint, bucket name, key, and optionally, a version. For example, in the URL

`https://MY-BUCKET.s3.us-west-2.amazonaws.com/my-prefix/my-file.jpg`,&#x20;

* `MY-BUCKET` is the name of the bucket
* &#x20;`/my-prefix/my-file.jpg` is the key
* `/my-prefix/` is a prefix (which you can think of as a "folder" inside a bucket)
