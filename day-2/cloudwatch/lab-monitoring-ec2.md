# LAB: Monitoring EC2

CloudWatch concepts might seem quite abstract at first, so let's see them in action.

We are going to&#x20;

* launch an EC2 instance in the my-vpc VPC
* create a dashboard that shows metrics for this instance&#x20;
* create an alarm that notifies us via email if the CPU utilization increases
* generate load to cause the CPU to spike&#x20;

Note: CloudWatch just rolled out a new visual layout, so your views may look different from some of the screenshots.&#x20;

## Launch an EC2 instance

Option: use the CLI.&#x20;

1. Go to the EC2 console and click Launch instance
2. We will use the free tier eligible Amazon Linux AMI&#x20;
3. For instance type we want whatever's free tier eligible
4. In instance details:
   1. For network, choose the VPC we created via the console **my-vpc**
   2. Subnet: pick the public one&#x20;
   3. Auto-assign public IP: enable
   4. Scroll down to Monitoring and check in "Enable CloudWatch detailed monitoring"
5. Click Review and Launch&#x20;
6. We can use the cli-keys.pem key pair we created in a previous lab
7. Launch&#x20;

Note: we have now enabled detailed monitoring for our EC2 instance. By default CloudWatch records data from EC2 with a 5-minute interval. You can optionally enable detailed monitoring that records data in 1-minute intervals, as we did. It costs extra (ballpark 2 euros per instance per month), but let's try it in this lab.

### Create a security group&#x20;

Security groups are VPC-specific. We haven't created one for the **my-vpc** yet and we are not able to use the security groups we made earlier as they are in the default VPC, so we must create a new one.&#x20;

1. In the EC2 console, go to Security groups
2. Create Security group
3. Security group name: ssh-sg
4. Description: SSH access in my-vpc
5. For VPC, pick my-vpc
6. Under inbound rules
   1. Add rule
   2. Type: SSH
   3. Source: My IP&#x20;
   4. Description is optional
7. Create security group&#x20;

### Attach security group to instance

1. Go to EC2 > Instances
2. Click on the instance we just launched&#x20;
3. Go to Actions > Security > change security groups
4. Remove the default security group first (bottom of the screen)
5. Then in Associated security groups, from the dropdown select the ssh-sg.
6. Click Add security group&#x20;
7. Click Save.

When you change the security group of a running instance, the changes take effect immediately. The console might sometimes take time to update, so you might see in the console that the security group of your instance has no inbound rules or something strange like that. Just wait and refresh the page, it should show the changes correctly then.&#x20;

We will leave the instance up and running and go to CloudWatch.&#x20;

## CloudWatch overview

### Automatic dashboard

Now let's go see what CloudWatch has to say about our running instance.&#x20;

1. Navigate to CloudWatch&#x20;
2. On the front page you will probably see CloudWatch: Overview
3. Click Overview and select EC2

That should take you to CloudWatch: EC2. Scroll to the very bottom of the page, where you should see EC2 instances.

![CloudWatch EC2 view](<../../.gitbook/assets/image (5).png>)

#### Generate some activity

Let's go back to the terminal where you've SSH'd into the EC2 instance. Let's run the following command:

`sudo yum update -y`

Let's go take a look at some graphs in CloudWatch. You may have to refresh the view using the refresh symbol on the upper right hand corner:

![Refresh symbol and CW graphs](<../../.gitbook/assets/image (74).png>)

As we can see from these graphs, we are getting information about network packets from our EC2 instance.&#x20;

#### Maximize graphs

If you hover over a graph, you can refresh it, maximize it or even download data as .csv (from the three dots).&#x20;

![Option to maximize graphs](<../../.gitbook/assets/image (238).png>)

### Metrics

Let's take a closer look at the metrics CloudWatch offers.&#x20;

1. Navigate to CloudWatch > Metrics
2. Scroll down and click on EC2

![EC2 in CW Metrics](<../../.gitbook/assets/image (260).png>)

Then click on Per-Instance Metrics.&#x20;

Find some metric names (for example NetworkIn and NetworkOut), click the triangle next to the name and choose "Add to graph".&#x20;

You can play around with different kinds of graphs (Line, pie, stacked) and time periods.&#x20;

### Metrics for memory?&#x20;

You might notice that the EC2 instance metrics cover things like network, CPU... but no memory consumption?&#x20;

We would need the Unified CloudWatch agent for that. Further reading:

[https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/metrics-collected-by-CloudWatch-agent.html](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/metrics-collected-by-CloudWatch-agent.html)

## Dashboard

We've seen automatic dashboards. Now let's create our own dashboard and populate it with graphs that are of interest to us.&#x20;

1. In CloudWatch, on the left side panel click Dashboards
2. Click Create dashboard
3. Give it a name my-ec2
4. Create dashboard

### First widget

1. A dialogue appears asking if you want to add a widget. Pick "Number".&#x20;
2. Next you will be asked to add a metric graph.&#x20;
   1. Pick EC2 metrics
   2. Per-instance metrics
   3. Pick CPUCreditUsage.&#x20;
3. Create widget.

I end up with a number that tells me what the CPUCreditUsage of my new EC2 instance is.

![New CloudWatch Number widget](<../../.gitbook/assets/image (360).png>)

### Second widget

Try creating a widget that shows the CPU utilization of the EC2 instance.&#x20;

1. Click Create widget
2. Pick "Line"&#x20;
3. EC2, per-instance metrics, CPUUtilization
4. Create widget

Once you've created the widgets, click Save dashboard.&#x20;

![Our modest EC2 dashboard](<../../.gitbook/assets/image (249).png>)

## Alarms

What if we wanted to get notified if our EC2 instance's CPU utilization suddenly spiked or say, went above 60%? This is where alarms come in.

Let's create an alarm that notifies us when CPU utilization goes above a certain threshold.&#x20;

1. In CloudWatch, from the left side panel click Alarms
2. You might see some auto-scaling alarms from our DynamoDB tables. Ignore them. You can hide them with the checkbox Hide Auto Scaling alarms.&#x20;
3. Click Create alarm
4. Click select metric
5. The usual EC2, per-instance metrics, CPUUtilization
6. Select metric
7. Next we configure the alarm. See what happens to the graph on your screen if you try different values for period. If you try to select something less than 1 minute, you get an error message saying the AWS/ namespaces only support periods greater than 60 seconds. Pick 1 minute for period.&#x20;
8. Under Conditions
   1. Threshold type: static
   2. Greater/equal
   3. than.... 60 (this will be a percentage)
   4. Click next
9. Under Notifications (we will discuss SNS later. For now just roll with it)
   1. Alarm state trigger: in alarm&#x20;
   2. Select an SNS topic: Create new topic
   3. Topic name: ec2-cpu-above-60
   4. Email endpoint: enter a valid email&#x20;
   5. Click Create topic
10. You'll notice we could have the alarm automatically stop, reboot or even terminate our EC2. We don't want that so click Remove.&#x20;
11. Click Next
12. Alarm name: ec2-cpu-above-60
13. Preview and create
14. Create alarm.

When you review the alarm you just created, you will see this:

![Warning](<../../.gitbook/assets/image (73).png>)

Go to your email inbox and look for email from AWS Notifications. In the email, click confirm subscription.&#x20;

We will now receive email alerts if the CPU utilization of our EC2 instance is 60% or greater.&#x20;

## Generate load

We will use a tool called stress to generate some CPU load.

### SSH into the instance

You can find the public IP of your instance from the EC2 console. To SSH into the instance, navigate to the directory where the cli-keys.pem keys are and run this command:

`ssh -i cli-keys.pem ec2-user@<your public IP>`&#x20;

### Install the stress tool&#x20;

Run these commands

```
sudo amazon-linux-extras install epel -y
sudo yum install stress -y
stress --version
```

This installs the stress tool on the instance [https://linux.die.net/man/1/stress](https://linux.die.net/man/1/stress)

Let's start to consume CPU with this command

`sudo stress --cpu 8`

### Monitor graphs in CloudWatch&#x20;

This feels redundant since we have an alarm set precisely so we don't have to sit and stare at graphs, but let's do it anyway.&#x20;

1. Go to CloudWatch&#x20;
2. Click the my-ec2 dashboard
3. Expand the CPUUtilization graph&#x20;
4. You can get it to auto-update:

![Set the graph to refresh ](<../../.gitbook/assets/image (332).png>)

It will take a little while for the CPU to start increasing. Eventually we do see an alarm:

![Alarm in CW](<../../.gitbook/assets/image (68).png>)

You should also receive an email notification.&#x20;

### Stop the stress tool

Go to the EC2 instance and type Ctrl+C to stop the stress tool.&#x20;

## Clean up&#x20;

Go ahead and&#x20;

* terminate the EC2 instance&#x20;
* delete the CW alarm
* delete the my-ec2 dashboard

To delete the SNS topic, navigate to SNS or simple notification service. Click topics, select the topic we created and delete. There is also a subscription to delete.&#x20;
