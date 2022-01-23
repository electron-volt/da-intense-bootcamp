# LAB: Lambda versions

## Versions in the console

Let's go back to the AWS console and navigate to Lambda. You should see two functions: let's pick the one we created first ("ConsoleLambda") via the console and click on it.&#x20;

### Version 1

Let's go to the code editor. We are now looking at and editing $LATEST. Let's change the string that the function outputs to "This is version 1".&#x20;

It should now look like this:

![version 1](<../../.gitbook/assets/image (225).png>)

Deploy your changes:

![changes deployed](<../../.gitbook/assets/image (366).png>)

Then on the right upper hand corner of the screen you'll see a dropdown that says "Actions". Select  "publish version".

A dialogue appears offering to give your version a description. You can type "version1".

You should see a green banner that says "Successfully created version 1 for function ConsoleLambda".

Go back to the code tab. What happens? You'll see you are not able to edit the code of this version: it is fixed. You are only able to edit the $LATEST.

What happens if you try to publish a new version without making changes to the code?&#x20;

### Version prod

Now suppose we wanted to make a different version of our function for production purposes.&#x20;

At the top of the screen you should see this:

![](<../../.gitbook/assets/image (356).png>)

Click on the name of your function to get back to $LATEST.

Edit the code to return "This is the production version" and deploy your changes.&#x20;

Then go to Actions and publish version "prod".&#x20;

### View your versions

Let's go back to $LATEST. Under "versions" you should see the following:

![Versions list](<../../.gitbook/assets/image (182).png>)
