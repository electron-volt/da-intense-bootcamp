# KMS

KMS or Key Management Service is a managed service that makes it easy for you to create and control the cryptographic keys that are used to protect your data.

For the Developer associate exam, you do not need to know very much about KMS. The most useful thing to know is S3 bucket encryption, so we will focus on that.&#x20;

## AWS KMS specific concepts

There are three kinds of keys:

* Customer managed key&#x20;
* AWS managed key
* AWS owned key

The KMS keys that you create are **customer managed keys.** You create, own and manage these keys and have complete control over them.&#x20;

AWS services that use KMS keys to encrypt your service resources often create keys for you. KMS keys that AWS services create in your AWS account are **AWS managed keys.** You can view, but not manage these keys.&#x20;

KMS keys that AWS services create in a service account are **AWS owned keys.** You cannot view or manage these keys. They can be used by AWS in several different AWS accounts.&#x20;

