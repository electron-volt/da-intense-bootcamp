# Application integration

In this section we will familiarize ourselves with the following services:

## SQS

Amazon Simple Queue Service (Amazon SQS) is a message queue. Messages sit in the queue until something polls the queue and **pulls** messages from it.&#x20;

SQS is commonly used by distributed applications to exchange messages through a polling model, and can be used to decouple sending and receiving components—without requiring each component to be concurrently available.

## SNS

Simple Notification Service is a publish-subscribe service. You configure an SNS topic and publish messages to the topic. Anyone subscribed to the topic will receive e.g. an SMS or email when the topic **pushes** your notification out.&#x20;

SNS allows applications to send time-critical messages to multiple subscribers through a “push” mechanism, eliminating the need to periodically check or “poll” for updates.

## Step Functions

Step Functions is a serverless orchestration service that lets you combine AWS Lambda functions and other AWS services to build business-critical applications. Through Step Functions' graphical console, you see your application’s workflow as a series of event-driven steps.

## Kinesis

Kinesis Data Streams, Kinesis Data Analytics and Kinesis Data Firehose. We won't cover Kinesis in this course. If you want to get certified, then it is useful to know on a high level what the different Kinesis services do. For example, suppose you want to monitor some hash tags used in social media.

* Kinesis Data Streams takes the real-time data feed from the social media
* Kinesis Data Analytics can be used to run SQL queries on the data in real-time
* Kinesis Data Firehose can be used to deliver the data to S3, Amazon Redshift (a data lake), Splunk or somewhere else for storage.&#x20;

There's also Kinesis for video, but that rarely shows up on the Developer associate exam.

For an overview of what these services do, see [https://aws.amazon.com/kinesis/](https://aws.amazon.com/kinesis/).

For more details, read the AWS documentation: [https://docs.aws.amazon.com/index.html](https://docs.aws.amazon.com/index.html)

