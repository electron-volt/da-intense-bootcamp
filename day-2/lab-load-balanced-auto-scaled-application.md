# LAB: Load balanced auto-scaled application

We got a big one here! We want to build the following;

![Graphics this good - you know I didn't make this myself](<../.gitbook/assets/image (110).png>)

We are going to create

* an ALB
* an Auto-scaling group&#x20;
* a launch template for a simple web server.

These components together will ensure that we have a highly available web server that is up and running and reachable no matter what. Even if an entire AZ goes down, our web server will still be serving content.&#x20;

### Default VPC

We will use the default VPC again and its default security group.&#x20;

Note: since we will use the same security group for all our EC2 instances and the ALB, those components will be able to communicate to one another.&#x20;

We will also want to allow HTTP traffic at least from our own IP Address so that we are able to use our browser to access the instances.&#x20;

## Modify launch template

The key difference between launch configurations (legacy) and templates is that you can modify a template. With launch configurations, if you wanted to change something you had to build a new one from scratch.&#x20;

Let's modify the test-template to include user data. This way all of our EC2 instances created by the ASG will be web servers.&#x20;

1. Go to EC2
2. Go to launch templates (I again had to go to launch config, then click the banner to view templates)
3. Modify the test-template

![Modify template](<../.gitbook/assets/image (183).png>)

For Template version description, write "includes user data".&#x20;

Scroll all the way down to Advanced details. At the very bottom you find User data. Paste this in:

{% code title="User data" %}
```
#!/bin/bash
yum update -y
yum install httpd -y
systemctl start httpd
systemctl enable httpd
cd /var/www/html
EC2_INSTANCE_ID="`wget -q -O - http://169.254.169.254/latest/meta-data/instance-id || die \"wget instance-id has failed: $?\"`"
echo "<h1>This is " $EC2_INSTANCE_ID "</h1>" > index.html
```
{% endcode %}

In the user data:

7\. we are using EC2 instance metadata to get the instance id of the running instance&#x20;

8\. we are putting together the string that the index.html site displays to the world.&#x20;

An example of what the result will look like:

![result of our user data script](<../.gitbook/assets/image (380).png>)

Once you have pasted the user data, click Create launch template (orange button). So no changes other than adding user data.

### Set default version

You are able to select a version of the launch template as the default:

![Set default version](<../.gitbook/assets/image (311).png>)

We want to use our new version with description "Includes user data" as default.&#x20;

## Create the load balancer

We want to have our ALB ready before we create the auto scaling.&#x20;

### Create a target group&#x20;

Create a target group.&#x20;

Target type: instance.

Name it **asg-tg**.&#x20;

Protocol HTTP, port 80.

VPC: default VPC.

Protocol version: HTTP1

Health check: use default settings

It does not have to have any targets in it, so just leave it empty and hit Create target group.&#x20;

### Create the ALB

Name it **last-lab**

Scheme: internet-facing

IP address: IPv4

Network mapping: default VPC. Select all available AZ's.&#x20;

Security group: use default sg for the default VPC.

Listener is HTTP : 80.&#x20;

Use the **asg-tg** for default action.&#x20;

![Default action](<../.gitbook/assets/image (190).png>)

Here is what you should have:

![summary](<../.gitbook/assets/image (145).png>)

Then "Create load balancer".&#x20;

## ASG

Now we will create our auto scaling group.&#x20;

Name: **last-lab-asg**

Template: **test-template** with the default version

Next.

Use the default VPC and pick all subnets.&#x20;

Instance type comes from the template, so no changes needed.&#x20;

Next.&#x20;

Load balancing: attach to an existing LB

Choose from your LB tg's

Pick the **asg-tg.**

For health checks:

EC2 is enabled by default. Also enable ELB health checks.&#x20;

Next.

Group size: put 2 in all boxes.&#x20;

Scaling policies: none.&#x20;

Now you can skip to review.

Create auto scaling group.&#x20;

Here is where we are now:

![ASG is getting ready](<../.gitbook/assets/image (86).png>)

## Monitor

Find the DNS name of your ALB and open it in a browser:

![ALB takes me to instance ...bdf](<../.gitbook/assets/image (299).png>)

That is indeed one of my EC2 instances:

![Instances launched by ASG](<../.gitbook/assets/image (156).png>)

### Stopping the instance&#x20;

Now I am going to stop the instance ..bdf. What I expect to have happen is this:

* the ASG realizes bdf is gone (EC2 health check)
* the ALB also realizes bdf is gone (ELB health check)
* ALB will begin to route traffic to my working instance ...69a
* ASG will replace bdf with a new instance.

Finally the mystery of the multiple health checks is resolved! Imagine what happened if we only had EC2 health checks. &#x20;

### ALB

The ALB is now routing all requests made to its DNS name to the only working EC2 instance:

![69a to the rescue! ](<../.gitbook/assets/image (41).png>)

### ASG

The ASG is also doing its part to keep us at two running instances:

![RIP ...bdf](<../.gitbook/assets/image (205).png>)



## Clean up

### Moral of the story&#x20;

In real life we don't actually go terminate or stop our own instances (on purpose anyway). We could set up our ASG to ensure that

* the CPU of our instances never goes above 60%&#x20;
* the HTTP requests to an instance never exceed 1000 req/s
* something else that is useful&#x20;

You can easily see the use cases for this if you had something in production running on EC2 instances.&#x20;

### Cleaning

Be sure to delete these:

* the ALB
* the ASG
* launch template
* target group

The EC2 instances are deleted automatically when the ASG is deleted.

