# LAB: Lambda alias

## Creating an alias&#x20;

Let's return to the Lambda console. Let's navigate to our version 2. Under General configuration, you will see Aliases:

![](<../../.gitbook/assets/image (84).png>)

Hit "create alias" and fill in the following:

![](<../../.gitbook/assets/image (326).png>)

A green banner that says "Alias prod was successfully created and is now pointing to version 2" is a sign of success.&#x20;

### ARN of an alias

You can see in the console that the ARN of this new alias is&#x20;

**arn:aws:lambda:aws-region:acct-id:function:MyLambdaFunction:prod.**

This is how customers would invoke your production function. You are free to change the version the alias points to - the ARN remains the same.&#x20;
