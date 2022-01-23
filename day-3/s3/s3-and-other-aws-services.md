# S3 and other AWS services

S3 is technically serverless, so why didn't we introduce it on day 1? Because S3 is often used in conjuction with other AWS services and I wanted to introduce some of them before S3. Here is a selection of AWS services that use S3.

## CloudFormation

As we have seen, CF templates get stored in S3. You may even recall seeing an option to use an S3 URL instead of uploading a template from your computer when we created a stack.

## CloudWatch

CloudWatch logs can be exported to S3.&#x20;

## Athena&#x20;

We have not discussed Athena, but it is an AWS service that you can use to query data in S3 with SQL queries.

## EBS

EBS snapshots are stored in S3. They are not accessible to you the user, but they are in S3 in the background.

## Lambda

Lambda and S3 are coupled in many ways: you can use S3 bucket notifications to trigger a Lambda function whenever a new object gets uploaded.&#x20;

There is also a feature in S3 called S3 Object Lambda. With S3 Object Lambda you can add your own code to S3 GET requests to modify and process data as it is returned to an application.

