# LAB: Create bucket

Navigate to the S3 console. You might find to your surprise that your account has two S3 buckets:

![Two S3 buckets](<../../.gitbook/assets/image (265).png>)

These buckets were created when we uploaded our CloudFormation templates. AWS needs somewhere to put them, so it has created a bucket for each template. CloudFormation does not delete these buckets when the stack is deleted to prevent accidental deletion of information.&#x20;

We can delete these buckets though since we will not be needing the templates anymore.&#x20;

#### Deleting a bucket

First empty the bucket. Two ways:

* in the Buckets list, select the bucket you want to empty and click Empty OR
* click the name of the bucket to view the contents. Check the box above the list of objects to select them all, then click Delete.&#x20;

You will have to confirm that you really want to delete all the objects.&#x20;

An empty bucket can be deleted from the Buckets list by selecting it and clicking Delete. Again you will be asked to confirm this (I guess people used to accidentally delete things and got mad at AWS..)&#x20;

## Create a bucket&#x20;

Now that our list of buckets is empty, we can start from a blank slate and create our own bucket.&#x20;

### In the console

1. Create bucket
2. Choose a name. It has to be globally unique, so maybe try your-name-ddmmyyyy
3. Pick a region&#x20;
4. Object ownership: ACLs disabled&#x20;
5. Block public access: block _all_ public access
6. Versioning: disable
7. No tags
8. No encryption&#x20;
9. Create bucket.&#x20;

### In the CLI&#x20;

Here is the command to create bucket eve-test2-10012022 in the eu-north-1 region:

`aws s3api create-bucket --bucket eve-test2-10012022 --region eu-north-1 --create-bucket-configuration LocationConstraint=eu-north-1`

Note: if you do not specify region and LocationConstraint, the bucket will get created in us-east-1. This is probably due to some strange historical reason.&#x20;

This command returns the following:

```json
{
    "Location": "http://eve-test2-10012022.s3.amazonaws.com/"
}
```

Now this shows why bucket names need to be globally unique: they are a part of the DNS name of the bucket.

### **Comparison**

Here are the two buckets:

![S3 buckets](<../../.gitbook/assets/image (200).png>)

Notice the difference in Access? "Objects can be public" does not mean that objects are public - it just means that they _might_ be. They are not explicitly kept from being public. I sound like a politician.

## Add a file

Let's try adding a file to both buckets.&#x20;

### In the console

Click the name of either bucket. Then select upload and put an object into the bucket.&#x20;

An object can be between 0 KB and 5 TB in size. The filetype can be anything: mp3, jpg, docx, iso, pdf, vmdk... to S3 it's just 1's and 0's.&#x20;

### In the CLI

Here is the command to upload file `s3_temp_file.txt` to bucket `eve-test-10012022`:

```
aws s3 cp s3_temp_file.txt s3://eve-test-10012022
```

## Clean up

Empty buckets don't cost anything. You only pay for the objects based on their storage class (topic of the next page) and the object size.&#x20;

We will need a bucket in a future lab, so please

* empty both buckets
* delete one of them.
