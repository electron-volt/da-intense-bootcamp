---
description: Serverless Application Model
---

# SAM

There is one more section to a CloudFormation template: Transforms.

The optional `Transform` section specifies one or more macros that AWS CloudFormation uses to process your template. The `Transform` section builds on the simple, declarative language of AWS CloudFormation with a powerful macro system.

AWS CloudFormation also supports _transforms_, which are macros hosted by AWS CloudFormation. An example of such a macro is AWS::Serverless.

## Serverless Application Model (SAM)

The AWS Serverless Application Model (AWS SAM) is an open-source framework that you can use to build serverless applications on AWS.

A **serverless application** is a combination of Lambda functions, event sources, and other resources that work together to perform tasks. Note that a serverless application is more than just a Lambda function—it can include additional resources such as APIs, databases, and event source mappings.

### AWS::Serverless

**AWS SAM template specification**. You use this specification to define your serverless application. It provides you with a simple and clean syntax to describe the functions, APIs, permissions, configurations, and events that make up a serverless application. You use an AWS SAM template file to operate on a single, deployable, versioned entity that's your serverless application.

### SAM CLI

**AWS SAM command line interface (AWS SAM CLI)**. You use this tool to build serverless applications that are defined by AWS SAM templates. The CLI provides commands that enable you to verify that AWS SAM template files are written according to the specification, invoke Lambda functions locally, step-through debug Lambda functions, package and deploy serverless applications to the AWS Cloud, and so on.

## Example

Creating a Lambda function with plain CloudFormation:

{% code title="Lambda with CF" %}
```yaml
Resources:
  LambdaWithCloudFormation:  
    Type: AWS::Lambda::Function
    Properties: 
      Handler: index.handler
      Runtime: nodejs12.x
```
{% endcode %}

And creating a Lambda function with the AWS::Serverless transform:

{% code title="Lambda with SAM" %}
```yaml
Transform: 'AWS::Serverless-2016-10-31'
Resources:
    LambdaWithSAM:
      Type: AWS::Serverless::Function
      Properties:
        Handler: index.handler
        Runtime: nodejs12.x
```
{% endcode %}

In this simple example one isn't much more complex than the other.
