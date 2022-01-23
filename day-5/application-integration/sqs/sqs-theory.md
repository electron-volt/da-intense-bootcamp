# SQS theory

We still have one SQS queue from the previous lab. Let's take a closer look at some of the options.&#x20;

1. Go to SQS and select your queue
2. Click edit.&#x20;

You will see these options:

![Editing a queue](<../../../.gitbook/assets/image (135).png>)

Let's take a closer look at each. You can also read more about these values by clicking the blue Info link next to the names.&#x20;

## Visibility timeout&#x20;

A message enters the queue. A consumer (EC2 instance, Lambda, whatever) begins to process the message. While the message is being processed, it is no longer visible to the other consumers.&#x20;

If the message is successfully processed, then the consumer deletes the message from the queue. But what happens if the message isn't successfully processed? It will reappear in the queue to be picked up by another processor.&#x20;

I considered drawing a picture but then I remembered what my graphics look like. 😂

The visibility timeout is the duration that the message is invisible to the other consumers while it is being processed. If you have large messages that are processed slowly, say it takes a few minutes, then you can increase the visibility timeout accordingly. A message can have a visibility timeout of 0 seconds up to 12 hours.&#x20;

## Message retention period

This one is rather easy. How long the messages stay in the queue. Values are 1 minute to 14 days.&#x20;

## Delivery delay

When messages appear in the poll, they can be delayed for some time before they become available to consumers. The delay can be between 0 seconds and 15 minutes.&#x20;

## Maximum message size

The maximum SQS message size is 256 KB. You can process up to 2 GB messages, but those have to be stored in S3 and you will need to use Amazon SQS Extended Client Library for Java.&#x20;

## Long vs short polling

AKA Receive message wait time.&#x20;

### Short polling

This is what AWS uses by default. Short polling is synonymous with receive message wait time being set to 0 (one of those just rolls off the tongue a little easier..)&#x20;

What this means is that SQS polls the queue and returns a result immediately. This can however lead to a situation where there _are_ messages in the queue, but polling the queue returns an empty response. With SQS you pay for each API call, so empty polls cost you extra money.&#x20;

### Long polling

Long polling means setting the receive message wait time to anything other than 0 (max is 20 seconds). In that case SQS polls the queue and waits for some time before it returns a response.&#x20;

This ensures that if the poll returns 0 messages, that is because there were no messages to return. Not because there were messages but the poll didn't have time to return them.&#x20;

Unless you have some reason why messages have to be polled from the queue as soon as they arrive, then long polling is typically a better choice.&#x20;

## The end

You can change these values and then send messages to your queue. This can all be done from within the SQS console.&#x20;

When you are done, go ahead and delete the queue.&#x20;
