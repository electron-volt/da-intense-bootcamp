# SQS

Amazon SQS or Simple Queue Service lets you integrate and decouple distributed software systems and components. Messages get published to a queue where they sit until some service polls the queue and pulls messages from it.&#x20;

There are two types of queues:

* Standard
* FIFO (first in, first out).&#x20;

The key difference between standard and FIFO is that in a FIFO queue message order is strictly preserved and messages are processed exactly once. In a standard queue ordering is "best effort" and messages get processed at least once (but possibly multiple times).&#x20;

## SQS and other AWS services

SQS pairs well with many technologies that we have already discussed. Here are two just to give some examples.

### SQS and Lambda

You can use an AWS Lambda function to process messages in an Amazon SQS queue. Lambda polls the queue and invokes your Lambda function synchronously with an event that contains queue messages. You can specify another queue to act as a _dead-letter queue_ for messages that your Lambda function can't process.

### SQS and EC2 auto-scaling&#x20;

Auto-scaling can be set to scale based on the number of messages in an SQS queue. If there is an EC2 backend that polls the queue, then the number of EC2 instances can be increased and decreased based on how many messages in the queue need processing.&#x20;

[https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-using-sqs-queue.html](https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-using-sqs-queue.html)

