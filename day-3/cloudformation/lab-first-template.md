# LAB: First template

In this lab we will use a very simple CloudFormation template to launch an EC2 instance.&#x20;

## Template

If you are in the Stockholm region eu-north, then the ECinstance.yml will work as-is and you do not need to change it.&#x20;

If you are in a different region:&#x20;

* You will need the AMI ID of the Amazon Linux 2 AMI&#x20;
* You will need to know which instance type is free-tier eligible.&#x20;

Change these values on lines 5 and 6.

Save the following on your computer as EC2instance.yml.&#x20;

{% code title="EC2instance.yml" %}
```yaml
Resources:    
  TestInstance:  
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-06bfd6343550d4a29
      InstanceType: t3.micro
```
{% endcode %}

## Create stack

Navigate to the CloudFormation console.&#x20;

Click Create stack.

Choose "Template is ready"&#x20;

Specify template: upload a template file&#x20;

Then upload the file we just created.

Click Next.

Give your stack a name like **test-instance** or whatever.&#x20;

Leave all other settings as they are and just click Next until you get to "create stack".&#x20;

## Monitor

In the CloudFormation console, you see all the events of your stack creation:

![Stack has been successfully created.](<../../.gitbook/assets/image (240).png>)

You can go to EC2 > instances to verify that an instance has been created:

![Instance is up and running](<../../.gitbook/assets/image (229).png>)

Back in the CloudFormation console, click the tab Resources:

![Resources show the instance](<../../.gitbook/assets/image (21).png>)

## Clean up&#x20;

Let's delete the stack. By deleting the stack, the instance also gets deleted.

Status changes to DELETE IN PROGRESS.

The Events tab shows the short-lived life of our instance and stack:

![Events tab in CF console.](<../../.gitbook/assets/image (152).png>)

