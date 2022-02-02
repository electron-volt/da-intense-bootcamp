# LAB: Web app

## Note: if images aren't loading..

The same material is available in GitHub

{% embed url="https://github.com/electron-volt/da-intense-bootcamp/blob/master/day-3/elastic-beanstalk/lab-web-app.md" %}
GitHub repo
{% endembed %}

No login is necessary, so you don't need a GitHub account.&#x20;

## Create application&#x20;

Without too much theory, let's go right ahead and see what we can do with Elbe.&#x20;

Navigate to the Elastic Beanstalk console.

The GUI has been recently changed, but you should be able to find an orange button that says "Create application". Go ahead and click on it.&#x20;

&#x20;

![GUI in January 2022](<../../.gitbook/assets/image (159).png>)

We are going to create a sample application using Python.

Elastic Beanstalk is going to create on our behalf

* An EC2 instance
* An Amazon EC2 security group
* An S3 bucket
* Amazon CloudWatch alarms
* An AWS CloudFormation stack
* A domain name.

Elbe itself is free to use, but we have to pay for the resources created.

## Create new environment

Environment tier: web server environment

Application name: my-test-app

Once you name your app, the environment information gets filled in for you.&#x20;

Platform: managed platform. We will go with Python.

Application code: sample application.&#x20;

Create environment.&#x20;

![Output from Elbe](<../../.gitbook/assets/image (219).png>)

Once the environment is up and running, you should see this:

![running environment](<../../.gitbook/assets/image (57).png>)



The navigation page on the left side of the console links to other pages that contain more detailed information about your environment and provide access to additional features:

* **Configuration** – Shows the resources provisioned for this environment, such as the Amazon Elastic Compute Cloud (Amazon EC2) instances that host your application. You can configure some of the provisioned resources on this page.
* **Health** – Shows the status of and detailed health information about the Amazon EC2 instances running your application.
* **Monitoring** – Shows statistics for the environment, such as average latency and CPU utilization. You can use this page to create alarms for the metrics that you are monitoring.
* **Events** – Shows information or error messages from the Elastic Beanstalk service and from other services whose resources this environment uses.
* **Tags** – Shows environment tags and allows you to manage them. Tags are key-value pairs that are applied to your environment.

## New version

Go ahead and download this zip [https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/samples/python.zip](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/samples/python.zip)

Unzip it, make whatever changes you want (maybe change the background color to #FFFF4081 and see what happens..), then zip it back up.&#x20;

Note: if you have problems with Elastic Beanstalk, then make sure that your resources are at the root of the ZIP. If the resources are inside a folder inside the ZIP, then your deployment may fail.

Let's go to Elbe.

Go to Application versions.

Upload > upload the new and improved python.zip.

### First taste of failure

My new version failed due to a hidden folder that AWS didn't like. &#x20;

Note to Mac users: Mac always adds hidden folders to zip's and AWS doesn't handle that well, so if you are zipping on a Mac, be sure to run the command below to remove the MACOS hidden folders:

```python
zip -d python.zip __MACOSX/\*
```

![new version](<../../.gitbook/assets/image (56).png>)

### OK back to the deployment!&#x20;

Then in Application versions, select the new version and under Actions choose deploy.&#x20;

Deploy to the environment Mytestapp-env.

![Update in progress](<../../.gitbook/assets/image (324).png>)

## Fail

My new version broke my environment. Rebooting all the servers didn't help.&#x20;

Were the hidden folders needed after all? This lab will be updated and improved later.

Anyway - we will see Elastic Beanstalk again at the end of day 4.&#x20;
