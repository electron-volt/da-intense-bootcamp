---
description: >-
  In this lab we will learn a new way of deploying Lambda functions: via the CLI
  with a .zip file.
---

# LAB: Lambda via the CLI

## Creating a Lambda function via the CLI&#x20;

Let's start by creating an execution role for your function. This role is what allows your function to access AWS resources, for example write logs to CloudWatch.&#x20;

### No CLI? No problem.

If you don't have the AWS CLI installed on your local machine, then you can use&#x20;

* AWS CloudShell
* AWS Cloud9 - more about this on day 4.&#x20;

CloudShell is a terminal that you can use via the browser. It doesn't work in all regions, but you can switch to a region where CloudShell is supported. Using it is very straightforward and you can google instructions to help you if you need.&#x20;

### Create an execution role

* [ ] Create a .json file with the following content and name it trust-policy.json

{% code title="trust-policy.json" %}
```json
{ 
    "Version": "2012-10-17", 
    "Statement": [
        {
            "Effect": "Allow", 
            "Principal": { 
                "Service": "lambda.amazonaws.com"
            }, 
            "Action": "sts:AssumeRole" 
        }
    ]
}
```
{% endcode %}

* [ ] Run the following command to create a role and associate the policy with the role&#x20;

```
aws iam create-role --role-name lambda-ex --assume-role-policy-document file://trust-policy.json
```

The output should look like this:

```json
{
    "Role": {
        "Path": "/",
        "RoleName": "lambda-ex",
        "RoleId": "AROA34F5GH6SOS",
        "Arn": "arn:aws:iam::123123123123:role/lambda-ex" ....
```

🎯 Note:  we will need the Arn of this role later.&#x20;

* [ ] Add basic Lambda execution permissions to the role with the following command

`aws iam attach-role-policy --role-name lambda-ex --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole`

You have now created an execution role called lambda-ex with basic execution permissions.&#x20;

### Prepare the .zip file

For this example we will be using Python.

* [ ] Create a file called `lambda_function.py`

The contents of this file are

{% code title="lambda_function.py" %}
```python
import json
def lambda_handler(event, context): 
    return { 
        'statusCode': 200, 
        'body': json.dumps('Hello from the cli lab!') 
    }import json
```
{% endcode %}

* [ ] Then create the .zip:

`zip my-deployment-package.zip lambda_function.py`

Your deployment package is now ready.

### Deploy the .zip file

We will deploy the .zip file via the CLI using the `aws lambda create-function` command. We need to provide the following information:

* **function name**: you can choose any name. E.g. MyLambdaFunction
* **zip file**: this is my-deployment-package.zip
* **handler**: for the Python example this is lambda\_function.lambda\_handler
* **runtime**: python3.9&#x20;
* **role**: remember the Arn of the lambda-ex role from before

The complete command looks like this:

`aws lambda create-function --function-name MyLambdaFunction --zip-file fileb://my-deployment-package.zip --role arn:aws:iam::123123123123:role/lambda-ex --handler lambda_function.lambda_handler --runtime python3.9`

You will get some JSON back as a response to a successfully run command.

### Test the function

Once the function has been deployed, we can test it via the CLI like this

`aws lambda invoke --function-name MyLambdaFunction --cli-binary-format raw-in-base64-out --payload '{ "key": "value" }' response.json`

The content of the payload does not matter in this case. You should get the following response:

```json
{
   "StatusCode": 200,
   "ExecutedVersion": "$LATEST"
}
```

To see some logs, we can run

`aws lambda invoke --function-name MyLambdaFunction out --log-type Tail`

The response should look like this:

```json
{ 
    "StatusCode": 200,
    "LogResult": base64-encoded string 
}
```

The logs aren't of much use to us if they are base64 encoded. To decode them, run

`aws lambda invoke --function-name MyLambdaFunction out --log-type Tail --query 'LogResult' --output text | base64 -d`

You get information about the duration, memory used and so on.&#x20;

### End result

&#x20;Try running

`aws lambda list-functions`

it should list two Lambda functions: the one we created via the console and the one we just created via the CLI.&#x20;
