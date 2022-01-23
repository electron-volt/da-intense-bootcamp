# EventBridge

EventBridge used to be called CloudWatch Events, which helps explain why it is featured in this section.

From AWS documentation:\
"Amazon EventBridge is a serverless event bus service that you can use to connect your applications with data from a variety of sources. EventBridge delivers a stream of real-time data from your applications, software as a service (SaaS) applications, and AWS services to targets such as AWS Lambda functions, HTTP invocation endpoints using API destinations, or event buses in other AWS accounts."

EventBridge receives an _event_, an indicator of a change in environment, and applies a rule to route the event to a target. Rules match events to targets based on either the structure of the event, called an _event pattern_, or on a schedule. For example, when an Amazon EC2 instance changes from pending to running, you can have a rule that sends the event to a Lambda function.

We will encounter EventBridge in a lab on day 5 when we look at Step Functions and serverless workflows.

More about EventBridge:

{% embed url="https://www.youtube.com/watch?v=28B4L1fnnGM" %}
