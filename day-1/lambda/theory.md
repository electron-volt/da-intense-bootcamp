# Theory

## Lambda theory: need-to-know

Now that we have created our first functions, let's stop to learn some Lambda theory. By now you should have some idea about the following concepts:

* handler&#x20;
* execution role
* testing&#x20;
* monitoring&#x20;

Now we will introduce two new concepts

* synchronous and asynchronous invocation
* versions

We will leave out many of the more complex details and focus on what you need to know to get started with Lambda and work with it in our future labs. If you are planning on getting certified, then the AWS documentation has everything you need to know: [https://docs.aws.amazon.com/lambda/latest/dg/welcome.html](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)

### Synchronous and asynchronous invocation

There are two ways to invoke your Lambda function:

**Synchronously**: When you invoke a function synchronously, Lambda runs the function and waits for a response. When the function completes, Lambda returns the response from the function's code.

**Asynchronously**: When you invoke a function asynchronously, you don't wait for a response from the function code. You hand off the event to Lambda and Lambda handles the rest.&#x20;

When we tested our functions in the console and CLI, we were using synchronous invocation.

Different AWS services invoke Lambda either synchronously or asynchronously. Some examples we will see later might be:

* S3 bucket event notifications invoke Lambda asynchronously
* DynamoDB streams used as an Event source mapping invoke Lambda synchronously

### Versions

In the previous lab, when we invoked our Lambda function it returned the following:

`{`

&#x20;   `"StatusCode": 200,`

&#x20;   `"ExecutedVersion": "$LATEST"`

`}`

Also recall how changes to the function's code in the console didn't take effect until the changes were deployed?&#x20;

The code you see in the code editor in the console is called $LATEST. This is the code you are able to edit. You make your changes and then deploy the changes. When the changes have been deployed, you will be able to create a new version of your code.&#x20;

A function version includes the following information:

* The function code and all associated dependencies.
* The Lambda runtime that invokes the function.
* All the function settings, including the environment variables.
* A unique Amazon Resource Name (ARN) to identify the specific version of the function.

Let's look at how Lambda versions are created in the console or the CLI.&#x20;
