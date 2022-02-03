# LAB: SNS Fanout

We will combine SNS and SQS into one lab. We are going to create

* an SNS topic
* two SQS queues

and subscribe both queues to the same topic. This is known as "fanout":

![SNS Fanout](<../../../.gitbook/assets/image (113).png>)

The _Fanout_ scenario is when a message published to an SNS topic is replicated and pushed to multiple endpoints, such as SQS queues. This allows for parallel asynchronous processing.

In this example, we have an imaginary web shop. A new customer creates an account and we want to use SNS to push a message into two different queues:

* one queue creates the account
* the other sends a welcome message to the new customer.&#x20;

We won't implement the actual back end as that would take too long.&#x20;

## The SNS topic

Let's first create our topic.

1. Navigate to SNS > Topics
2. Click create topic
3. Choose Standard (if our use case called for a FIFO SQS queue, then we could also use a FIFO topic).&#x20;
4. For name, put **fanout-lab**
5. Skip the other parts
6. Click create.&#x20;

That's all!

## The SQS queues

Now let's create our queues. First queue will be for our imaginary user account creation.&#x20;

### Create the queue

1. Navigate to SQS&#x20;
2. Click Create queue
3. Pick Standard queue
4. Name: **Accounts**
5. Create

There are lots of options like delay, visibility timeout and so on: just keep the defaults. We will break these down soon.&#x20;

### Subscribe to SNS topic

Once your queue has been created, you will be shown an overview of the queue's information. You can subscribe to an SNS topic at the bottom of the screen.&#x20;

Click Subscribe to Amazon SNS topic. A dialogue appears. Select the fanout-lab topic from the dropdown and click Save.&#x20;

### Second queue

Repeat the exact same steps, but name the queue **Welcome**. Subscribe to the same topic.&#x20;

## Publish message

Back in the **SNS** console, you will see a button Publish message

![you might even see two ](<../../../.gitbook/assets/image (47).png>)

Let's publish a message and then verify that it appears in both our SQS queues.

Subject: new-account

Message body: make your own or use this

```
{
  "id": 10691,
  "name": "Bruce Wayne",
  "address": "Batcave, Gotham",
  "email": "ceo@wayneenterprises.com"
}
```

Here is what it looks like, with some attributes added:

![message to be published](<../../../.gitbook/assets/image (92).png>)

Click Publish message.&#x20;

Go ahead and send a few more. The content can be anything.&#x20;

## Poll the queues

Let's go to SQS and see if the messages appear in our queues.

1. Go to SQS
2. pick either queue
3. click Send and receive messages

You'll see this (I published 3 messages to my SNS topic):

![No messages have been polled](<../../../.gitbook/assets/image (286).png>)

You can see there are 3 messages available, but none are displayed at the bottom of the screen. This illustrates that SQS does not _deliver_ messages, but they have to be explicitly pulled from the queue by polling for them to go anywhere.&#x20;

Click poll for messages.&#x20;

![Polling is in progress](<../../../.gitbook/assets/image (276).png>)

Click on any of the messages to view the content:

![message and attributes](<../../../.gitbook/assets/image (195).png>)

You can also go poll the other queue to see that the same messages appear there as well.&#x20;

## Conclusion and clean up

In real life we would not sit at the console polling the queue ourselves. We would have Fargate or EC2 instances or Lambda or something else that polls the queue and automatically processes the messages.&#x20;

### Cleaning&#x20;

Go ahead and delete the SNS topic and both subscriptions.

Delete **one** of the SQS queues - we can use one of them in the other section to examine settings.
