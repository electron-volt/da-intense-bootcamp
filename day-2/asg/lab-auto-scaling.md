# LAB: Auto-scaling

In this lab we are going to create&#x20;

* a launch template that launches the same Amazon Linux EC2 instances that we have been using all day&#x20;
* an auto-scaling group that ensures that we always have at least and at most one instance running at all times.&#x20;

We will have the auto-scaling group create an instance. We will then terminate the instance (what a waste of resources) and watch as the ASG automatically creates a new one.&#x20;

## VPC

In this lab we will be working with the default VPC. It already has a public subnet in every AZ, with auto-assign public IP enabled.&#x20;

It is useful to know&#x20;

* the VPC ID of the default VPC, or at least the last 3 characters of the ID
* the ID of the default security group of the default VPC.&#x20;

You can find this information from the console.

## Create a launch template

In my AWS console, there was no option available for launch templates. I clicked Create launch configuration, then got a banner saying that AWS instead recommend launch templates and then I was able to create launch templates. AWS keep changing the GUI so this may have changed by the time we do this lab.&#x20;

1. Create launch template
2. Name: **test-template**
3. Check the box next to Auto Scaling guidance
4. For Application and OS images, choose
   1. Amazon Linux
   2. Amazon Linux 2 AMI
   3. 64-bit x86&#x20;
5. For instance type, pick the one that is free-tier eligible
6. For key-pairs, choose **cli-keys** (or day2labs, it makes no difference)
7. For network settings,&#x20;
   1. For subnet: don't include in launch template (we will let the ASG decide)
   2. For security group: choose the default security group of the default VPC
8. For storage: keep the defaults
9. Skip Tags and advanced details
10. Click Create launch template



## Create an auto-scaling group&#x20;

1. Click Create auto-scaling group&#x20;
2. Name: **test-asg**
3. Launch template: choose test-template
4. Click Next
5. Network: the default VPC
6. For subnets, click all of them
7. Skip instance type requirements: these come from the launch template
8. Click Next
9. For Load balancing: No load balancer
10. Leave other settings as they are. Click Next.&#x20;
11. Group size: Desired/minimum/maximum are all 1.&#x20;
12. Scaling policies: None
13. You can skip all the other sections. Go to review and create.&#x20;

The ASG has been created.&#x20;

![The test-asg.](<../../.gitbook/assets/image (131).png>)

## Test the ASG

### Verify that it worked

Give the ASG some time to work its magic and then go see the list of EC2 instances. There is an instance running!&#x20;

### Terminate the instance

Let's manually terminate the EC2 instance. The auto-scaling group will do the following:&#x20;

* it will detect that we do not have a running instance&#x20;
* it will realize that our minimum and desired capacity is 1
* it will realize that 0 is not 1&#x20;
* it will launch a new EC2 instance

You can watch this happen if you go to Auto Scaling > Auto Scaling Groups > click on test-asg and the Activity tab.&#x20;

Keep refreshing Activity history. You will see the ASG launches a new instance. &#x20;

![ASG working hard to undo my sabotage](<../../.gitbook/assets/image (11).png>)



## Clean up

Delete the **test-asg** ASG. You will notice the new EC2 instance is also terminated.&#x20;

The launch template does not need to be deleted, as we can use it in the final lab of the day.&#x20;
