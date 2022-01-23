---
description: In this lab we will create a Lambda function via the AWS console.
---

# LAB: Create a Lambda function

## Creating a Lambda function via the console&#x20;

We will create our first Lambda function via the AWS console.

* [ ] Log in to your AWS account as the admin user (not root)
* [ ] Navigate to AWS Lambda

Take a look at the upper right hand corner to see which region you are in. Change your location to the same region that you used when setting up the CLI.&#x20;

### Create the function

* [ ] Press "Create Function"&#x20;
* [ ] For this simple lab, we can choose "Author from scratch"&#x20;
* [ ] Give your function a name, e.g. consoleLambda
* [ ] Choose a runtime. I recommend Python 3.9.&#x20;
* [ ] For architecture, pick x86.

![creating a Lambda](<../../.gitbook/assets/image (383).png>)

### Create an execution role

Next you will be asked for an execution role for your Lambda function. If you select option "Create a new role with basic Lambda permissions", Lambda will create an execution role named \<yourfunctionname>-role-\<random>.&#x20;

![execution role for the function](<../../.gitbook/assets/image (217).png>)

💡 Info: Lambda will offer to create a new role for each function. If you have a dozen functions, your IAM role listing will have a dozen roles with gibberish names. Deleting the Lambda function will not delete the corresponding role. So you may ask, why not have just one role called generic-lambda-role and use that for all your functions? For personal testing purposes and functions that do nothing more than print Hello World, this could be done. However it's prudent to follow the **principle of least privilege** and give every function its own role. Optional reading: [https://www.trendmicro.com/cloudoneconformity/knowledge-base/aws/Lambda/sharing-an-iam-role-within-more-than-one-lambda-function.html](https://www.trendmicro.com/cloudoneconformity/knowledge-base/aws/Lambda/sharing-an-iam-role-within-more-than-one-lambda-function.html)

* [ ] We ignore Advanced settings for now. Press "Create function"

Wait as AWS creates the function and IAM role.

### Testing Lambda functions

You'll be taken to the function overview. You can see the function code in Code source. We have a simple function that prints out "Hello from Lambda!".

![Lambda handler](<../../.gitbook/assets/image (33).png>)

🎯 Note: notice the Lambda handler in your code? The Lambda function _handler_ is the method in your function code that processes events. When your function is invoked, Lambda runs the handler method.&#x20;

#### Create test event

Above the source code there is a button that says Test. Press it.

* [ ] Give your test event a name (any name), leave all other settings as they are&#x20;
* [ ] Press "Save"

![test event](<../../.gitbook/assets/image (328).png>)

#### Test the function

Now if you press "Test" again, another tab appears in the source code editor: execution results:

![execution results](<../../.gitbook/assets/image (130).png>)

If you click open the execution results tab in the code source, you can see the output:

```
Test Event Name 
<the name you chose>
Response 
{ 
     "statusCode": 200, 
     "body": ""Hello from Lambda!""
}
```

In this case the contents of the test event are irrelevant, as the Lambda function code doesn't take any input and just ignores the test event.&#x20;

If we had a function that took some input, then we could use the test event to mock that input.&#x20;

### Edit the function

Next let's go back to our handler and change something.&#x20;

![changes have not been deployed](<../../.gitbook/assets/image (215).png>)

* [ ] Let's replace the "Hello from Lambda" with our own text: it can be anything, like "look mom I can Lambda".
* [ ] Press "Test" again

Wait, the execution results still show the old text!&#x20;

🎯This is because changes to the function need to be deployed first.&#x20;

### Deploy the function

* [ ] Press "Deploy"&#x20;
* [ ] Press "Test" again

![changes have been deployed](<../../.gitbook/assets/image (24).png>)

Now the execution results show your chosen text instead of the default.&#x20;

![test results after deploy](<../../.gitbook/assets/image (103).png>)

### Monitor the function

Above code source you'll see tabs like Code, Test and Monitor. Let's click on Monitor.&#x20;

You will see many graphs detailing the number of invocations, duration and many other metrics. If you don't see any data, go back to your function and hit "test" a few more times to generate some data.&#x20;

We will go into much more detail about monitoring and AWS CloudWatch later in the course, but for now this is all you need to know about monitoring your function.&#x20;

![](<../../.gitbook/assets/image (357).png>)

### End result

There you have it, your first AWS Lambda function! Let's move on to the next lab. &#x20;
