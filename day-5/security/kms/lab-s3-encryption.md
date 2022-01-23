# LAB: S3 encryption

Data encryption in S3 breaks down into two options:

* Client-side encryption - that is, the file is already encrypted before it is uploaded to S3
* Server-side encryption - the file is uploaded to S3 and S3 handles the encryption. This is what we will focus on.&#x20;

## Server-side Encryption (SSE) in S3

There are three kinds of SSE in S3:

### SSE-S3

Server-side encryption with S3-managed keys. There are no additional fees to use SSE-S3 (just standard S3 request charges). In most cases this is a good way to do S3 encryption.&#x20;

If you need server-side encryption for all of the objects that are stored in a bucket, use a **bucket** **policy**. For example, use the bucket policy to deny permissions to upload an object unless the request includes the `x-amz-server-side-encryption` header to request server-side encryption (good exam tip).&#x20;

### SSE-KMS

Server-Side Encryption with AWS KMS keys. There are additional charges for using KMS keys. You can cut costs by using bucket-level keys: you don't have a different key for each object, but one key for the bucket. This reduces API calls from S3 to KMS, saving you money.&#x20;

AWS KMS uses _envelope encryption_. Envelope encryption is the practice of encrypting your plaintext data with a data key, and then encrypting that data key with a root key.

### SSE-C

Server-Side Encryption with Customer-Provided Keys. You the customer manage the keys,  AWS manages the encryption. There may be some regulation that states that you have to have complete ownership of the cryptographic materials - in that case SSE-C is a good fit.

## Enabling SSE-S3

### Using the CLI&#x20;

Create a bucket (in eu-north-1):

```
aws s3api create-bucket --bucket PUT-A-NAME-HERE --region eu-north-1 --create-bucket-configuration LocationConstraint=eu-north-1
```

Then enable encryption:

```
aws s3api put-bucket-encryption --bucket NAME-HERE --server-side-encryption-configuration '{
    "Rules": [
        {
            "ApplyServerSideEncryptionByDefault": {
                "SSEAlgorithm": "AES256"
            }
        }
    ]
}'
```

I cannot believe this bucket name was available:

![so not ever deleting this bucket](<../../../.gitbook/assets/image (29).png>)

Click the bucket name, then go to the Properties tab and scroll down to verify that SSE-S3 is now on:

![it worked.](<../../../.gitbook/assets/image (165).png>)

### Using the console

Click create bucket.

In the dialogue there is a section called Default encryption. When you click the radio button for Enable, you see this:

![](<../../../.gitbook/assets/image (148).png>)

## Clean up&#x20;

Empty buckets do not cost anything and SSE-S3 has no additional costs, so cleaning up is optional.
