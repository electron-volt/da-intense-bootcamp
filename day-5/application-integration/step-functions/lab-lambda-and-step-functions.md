---
description: >-
  Handle Errors in Serverless Applications with AWS Step Functions and AWS
  Lambda
---

# LAB: Lambda and Step Functions

## Walk-through <a href="#handle_errors_in_serverless_applications" id="handle_errors_in_serverless_applications"></a>

1. Create a Lambda function that mocks an API&#x20;
2. Create a state machine in Step Functions
3. Test the state machine.

## Create the Lambda

Hello Lambda, our old friend.

Create function. Choose **Author from scratch**.

For **Name**, type _MockAPIFunction_.\
For **Runtime**, choose **Python 3.6**.\
For **Role**, select **Create new role with basic Lambda permissions.**

Click **Create function**.

The function code:

```python
class TooManyRequestsException(Exception): pass
class ServerUnavailableException(Exception): pass
class UnknownException(Exception): pass

def lambda_handler(event, context):
    statuscode = event["statuscode"]    
    if statuscode == "429":
        raise TooManyRequestsException('429 Too Many Requests')
    elif statuscode == "503":
        raise ServerUnavailableException('503 Server Unavailable')
    elif statuscode == "200":
        return '200 OK'
    else:
        raise UnknownException('Unknown error')
```

Note: you will need the Lambda function ARN later.

## Create a Step Functions State Machine

In this step, you’ll use the Step Functions console to create a state machine that uses a Task state with a Retry and Catch field to handle the various API response codes. You’ll use a Task state to invoke you mock API Lambda function, which will return the API status code you provide as input into your state machine.

Go to Step Functions.

On the **Create a state machine** page, select **Write your workflow in code.**

Type**: standard.**

Replace the contents of the **Definition** section with the following code. On line 7, you will need the ARN of your Lambda function.&#x20;

```json
{
  "Comment": "An example of using retry and catch to handle API responses",
  "StartAt": "Call API",
  "States": {
    "Call API": {
      "Type": "Task",
      "Resource": ---------LAMBDA ARN GOES HERE IN QUOTES--------------,
      "Next" : "OK",
      "Comment": "Catch a 429 (Too many requests) API exception, and resubmit the failed request in a rate-limiting fashion.",
      "Retry" : [ {
        "ErrorEquals": [ "TooManyRequestsException" ],
        "IntervalSeconds": 1,
        "MaxAttempts": 2
      } ],
      "Catch": [ 
        {
          "ErrorEquals": ["TooManyRequestsException"],
          "Next": "Wait and Try Later"
        }, {
          "ErrorEquals": ["ServerUnavailableException"],
          "Next": "Server Unavailable"
        }, {
          "ErrorEquals": ["States.ALL"],
          "Next": "Catch All"
        }
      ]
    },
    "Wait and Try Later": {
      "Type": "Wait",
      "Seconds" : 1,
      "Next" : "Change to 200"
    },
    "Server Unavailable": {
      "Type": "Fail",
      "Error":"ServerUnavailable",
      "Cause": "The server is currently unable to handle the request."
    },
    "Catch All": {
      "Type": "Fail",
      "Cause": "Unknown error!",
      "Error": "An error of unknown type occurred"
    },
    "Change to 200": {
      "Type": "Pass",
      "Result": {"statuscode" :"200"} ,
      "Next": "Call API"
    },
    "OK": {
      "Type": "Pass",
      "Result": "The request has succeeded.",
      "End": true
    }
  }
}
```

You should see a visualization of the workflow automatically. If not, click refresh.

![Visual workflow.](<../../../.gitbook/assets/image (136).png>)

After reviewing the visual workflow, click **Next**.

Name: MyAPIStateMachine.

Permissions: **choose an existing role**. Pick the _step\_functions\_basic\_execution._

Create the state machine.&#x20;

## Execute the state machine

Next we want to execute our state machine. Click **create new execution.**&#x20;

This dialogue appears:

![New execution](<../../../.gitbook/assets/image (185).png>)

For input, use the following:

```json
{"statuscode": "200"}
```

Then start the execution.&#x20;

You can view the Graph inspector and click on various stages:

![Status code 200 execution. ](<../../../.gitbook/assets/image (46).png>)

### Other status codes

Repeat the steps of state execution, but use as input status codes&#x20;

* 429
* 503
* any status code other than 200, 429 or 503. For example 310 or something fictional like 666.

## Clean up&#x20;

Terminate the state machine and delete the Lambda function.&#x20;

Delete the IAM roles for Step functions and Lambda.





