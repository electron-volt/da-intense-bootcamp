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

See "quick option" at the bottom of the page if you're in a hurry and want to use CloudFormation instead.&#x20;

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

Delete **one** of the SQS queues - we can use one of them in the other section to examine settings.&#x20;

## Quick option

At the bottom of the page there is a CloudFormation template that achieves a similar result: an SNS topic and two SQS queues. It also creates two user accounts with passwords, so not exactly the same lab.&#x20;

Or you can just watch an AWS tech talk:

{% embed url="https://www.youtube.com/watch?v=UesxWuZMZqI" %}

{% code title="fanout-template.json" %}
```json
{
  "AWSTemplateFormatVersion" : "2010-09-09",
  
  "Description" : "AWS CloudFormation Sample Template SNSToSQS: This Template creates an SNS topic that can send messages to 
  two SQS queues with appropriate permissions for one IAM user to publish to the topic and another to read messages from the queues. 
  MySNSTopic is set up to publish to two subscribed endpoints, which are two SQS queues (MyQueue1 and MyQueue2). MyPublishUser is an IAM user 
  that can publish to MySNSTopic using the Publish API. MyTopicPolicy assigns that permission to MyPublishUser. MyQueueUser is an IAM user 
  that can read messages from the two SQS queues. MyQueuePolicy assigns those permissions to MyQueueUser. It also assigns permission for 
  MySNSTopic to publish its notifications to the two queues. The template creates access keys for the two IAM users with MyPublishUserKey 
  and MyQueueUserKey. ***Warning*** you will be billed for the AWS resources used if you create a stack from this template.",

  "Parameters": {
    "MyPublishUserPassword": {
      "NoEcho": "true",
      "Type": "String",
      "Description": "Password for the IAM user MyPublishUser",
      "MinLength": "1",
      "MaxLength": "41",
      "AllowedPattern": "[a-zA-Z0-9]*",
      "ConstraintDescription": "must contain only alphanumeric characters."
    },
    "MyQueueUserPassword": {
      "NoEcho": "true",
      "Type": "String",
      "Description": "Password for the IAM user MyQueueUser",
      "MinLength": "1",
      "MaxLength": "41",
      "AllowedPattern": "[a-zA-Z0-9]*",
      "ConstraintDescription": "must contain only alphanumeric characters."
    }
  },


  "Resources": {
    "MySNSTopic": {
      "Type": "AWS::SNS::Topic",
      "Properties": {
        "Subscription": [{
            "Endpoint": {
              "Fn::GetAtt": ["MyQueue1", "Arn"]
            },
            "Protocol": "sqs"
          },
          {
            "Endpoint": {
              "Fn::GetAtt": ["MyQueue2", "Arn"]
            },
            "Protocol": "sqs"
          }
        ]
      }
    },
    "MyQueue1": {
      "Type": "AWS::SQS::Queue"
    },
    "MyQueue2": {
      "Type": "AWS::SQS::Queue"
    },
    "MyPublishUser": {
      "Type": "AWS::IAM::User",
      "Properties": {
        "LoginProfile": {
          "Password": {
            "Ref": "MyPublishUserPassword"
          }
        }
      }
    },
    "MyPublishUserKey": {
      "Type": "AWS::IAM::AccessKey",
      "Properties": {
        "UserName": {
          "Ref": "MyPublishUser"
        }
      }
    },
    "MyPublishTopicGroup": {
      "Type": "AWS::IAM::Group",
      "Properties": {
        "Policies": [{
          "PolicyName": "MyTopicGroupPolicy",
          "PolicyDocument": {
            "Statement": [{
              "Effect": "Allow",
              "Action": [
                "sns:Publish"
              ],
              "Resource": {
                "Ref": "MySNSTopic"
              }
            }]
          }
        }]
      }
    },
    "AddUserToMyPublishTopicGroup": {
      "Type": "AWS::IAM::UserToGroupAddition",
      "Properties": {
        "GroupName": {
          "Ref": "MyPublishTopicGroup"
        },
        "Users": [{
          "Ref": "MyPublishUser"
        }]
      }
    },
    "MyQueueUser": {
      "Type": "AWS::IAM::User",
      "Properties": {
        "LoginProfile": {
          "Password": {
            "Ref": "MyQueueUserPassword"
          }
        }
      }
    },
    "MyQueueUserKey": {
      "Type": "AWS::IAM::AccessKey",
      "Properties": {
        "UserName": {
          "Ref": "MyQueueUser"
        }
      }
    },
    "MyRDMessageQueueGroup": {
      "Type": "AWS::IAM::Group",
      "Properties": {
        "Policies": [{
          "PolicyName": "MyQueueGroupPolicy",
          "PolicyDocument": {
            "Statement": [{
              "Effect": "Allow",
              "Action": [
                "sqs:DeleteMessage",
                "sqs:ReceiveMessage"
              ],
              "Resource": [{
                  "Fn::GetAtt": ["MyQueue1", "Arn"]
                },
                {
                  "Fn::GetAtt": ["MyQueue2", "Arn"]
                }
              ]
            }]
          }
        }]
      }
    },
    "AddUserToMyQueueGroup": {
      "Type": "AWS::IAM::UserToGroupAddition",
      "Properties": {
        "GroupName": {
          "Ref": "MyRDMessageQueueGroup"
        },
        "Users": [{
          "Ref": "MyQueueUser"
        }]
      }
    },
    "MyQueuePolicy": {
      "Type": "AWS::SQS::QueuePolicy",
      "Properties": {
        "PolicyDocument": {
          "Statement": [{
            "Effect": "Allow",
            "Principal": {
              "Service": "sns.amazonaws.com"
            },
            "Action": ["sqs:SendMessage"],
            "Resource": "*",
            "Condition": {
              "ArnEquals": {
                "aws:SourceArn": {
                  "Ref": "MySNSTopic"
                }
              }
            }
          }]
        },
        "Queues": [{
          "Ref": "MyQueue1"
        }, {
          "Ref": "MyQueue2"
        }]
      }
    }
  },
  "Outputs": {
    "MySNSTopicTopicARN": {
      "Value": {
        "Ref": "MySNSTopic"
      }
    },
    "MyQueue1Info": {
      "Value": {
        "Fn::Join": [
          " ",
          [
            "ARN:",
            {
              "Fn::GetAtt": ["MyQueue1", "Arn"]
            },
            "URL:",
            {
              "Ref": "MyQueue1"
            }
          ]
        ]
      }
    },
    "MyQueue2Info": {
      "Value": {
        "Fn::Join": [
          " ",
          [
            "ARN:",
            {
              "Fn::GetAtt": ["MyQueue2", "Arn"]
            },
            "URL:",
            {
              "Ref": "MyQueue2"
            }
          ]
        ]
      }
    },
    "MyPublishUserInfo": {
      "Value": {
        "Fn::Join": [
          " ",
          [
            "ARN:",
            {
              "Fn::GetAtt": ["MyPublishUser", "Arn"]
            },
            "Access Key:",
            {
              "Ref": "MyPublishUserKey"
            },
            "Secret Key:",
            {
              "Fn::GetAtt": ["MyPublishUserKey", "SecretAccessKey"]
            }
          ]
        ]
      }
    },
    "MyQueueUserInfo": {
      "Value": {
        "Fn::Join": [
          " ",
          [
            "ARN:",
            {
              "Fn::GetAtt": ["MyQueueUser", "Arn"]
            },
            "Access Key:",
            {
              "Ref": "MyQueueUserKey"
            },
            "Secret Key:",
            {
              "Fn::GetAtt": ["MyQueueUserKey", "SecretAccessKey"]
            }
          ]
        ]
      }
    }
  }
}
```
{% endcode %}

When creating the stack, you will be prompted for two passwords.

![stack creation](<../../../.gitbook/assets/image (69).png>)
