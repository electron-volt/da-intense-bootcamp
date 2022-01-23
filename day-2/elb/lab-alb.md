---
description: LBA, ABL, BAL, BLA
---

# LAB: ALB

We are going to create an internet facing application load balancer. The idea is that clients send requests over the internet and the ALB forwards them to instances in our public subnets.&#x20;

This is quite a long lab, so let's talk it over first:

* we will set up the VPC that we will be working with&#x20;
* we will create another public subnet
* we will launch two EC2 instances, in different subnets
* we will create two target groups and put each EC2 instance in a different target group
* we will create the ALB
* we will have the ALB forward certain kind of traffic to different instances.

We want to use path-based routing where requests go to different instances based on the path. &#x20;

![ALB and path-based routing](<../../.gitbook/assets/image (12).png>)

## Prepare the VPC

The internet facing ALB requires that we have at least two availability zones (e.g. 1a and 1b) and that both AZ's have at least 1 public subnet. To make working with these resources easier, let's give them names.

### Name the VPC

We will be working with the VPC that we created using the CLI in a previous lab.&#x20;

1. Go to VPC > Your VPCs
2. There should be three VPCs:&#x20;
   1. the default VPC (CIDR starts with octet 172)
   2. the my-vpc
   3. a third one with CIDR block 10.1.0.0/16 - this is the one we want
3. Hover over the - in the name column. Click on it.
4. Name the VPC **alb-vpc.**

![alb-vpc has been given a name](<../../.gitbook/assets/image (174).png>)

### Name the route table

Now let's find the route table associated with the 10.1.2.0/24 public subnet and name it alb-public-rt.&#x20;

1. In the VPC service, click Route tables
2. Find the route table in the **alb-vpc** that has an explicit subnet association (this will be the route table that has the route to IGW in it).&#x20;
3. Name that route table **alb-public-rt.**

### **Name the subnet**

Once the route table has been named, we need to find out which AZ our public subnet is in.&#x20;

1. In the VPC service, go to subnets
2. Find the subnet that is in VPC **alb-vpc** and has CIDR 10.1.2.0/24
3. Find out which AZ it is in (for example eu-north-1**a**)
4. Then name the subnet **alb-public-a.**

![alb-public-a subnet](<../../.gitbook/assets/image (40).png>)

### Create new subnet

This **alb-vpc** VPC has two subnets that we created in a previous lab:

* 10.1.1.0/24 (private)
* 10.1.2.0/24 (public).&#x20;

The catch is this: our internet-facing ALB will need two public subnets, in _different_ AZ's. The subnets above (in my lab anyway) are both in the same subnet. This is what AWS says about the create-subnet command:

"If you create more than one subnet in your VPC, we do not necessarily select a different zone for each subnet."&#x20;

Our job is then to create another subnet in a different AZ and make it public.

1. Go to VPC
2. Go to Subnets
3. Click create subnet
4. VPC ID: **alb-vpc**
5. For subnet 1 of 1:
   1. Subnet name: **alb-public-N** where N is not a (I will use c)&#x20;
   2. From the AZ dropdown, pick the AZ that ends in c
   3. CIDR block: 10.1.3.0/24
6. Create subnet.

![two subnets in the alb-vpc](<../../.gitbook/assets/image (275).png>)

#### Make the new subnet public

One more step: we want to make the new subnet public.&#x20;

1. Click on the subnet ID of the newly created subnet **alb-public-c**
2. Scroll down and click Route table
3. Click "edit route table association"
4. In the route table ID dropdown, select the **alb-public-rt**
5. Save.

Here is what we have now (the diagram doesn't show the private 10.1.1.0/24 subnet in the VPC):

![Our VPC and its public subnets](<../../.gitbook/assets/image (267).png>)

#### Enable auto-assign public IP

We want for EC2 instances that are launched in these subnets to get public IP addresses.

1. Go to subnets
2. Click on the subnet ID of **alb-public-a**
3. Under actions choose edit subnet settings
4. Check the box next to Enable auto-assign public IPv4 address
5. Click save.

Repeat for **alb-public-c.**

## Launch EC2 web servers

We want to launch two EC2 web servers: one in each public subnet.&#x20;

### First instance&#x20;

1. In the EC2 console, launch an instance.&#x20;
2. AMI: Amazon Linux 2
3. Instance type: whatever's free
4. On instance details: Network is **alb-vpc**, subnet is **alb-public-a**

![Network settings for first instance](<../../.gitbook/assets/image (77).png>)

5\. In user data at the bottom of the screen, paste the following:

{% code title="User data " %}
```
#!/bin/bash 
yum update -y 
yum install httpd -y 
systemctl start httpd 
cd /var/www/html
echo "<h1>This is instance web-a</h1>" > index.html
systemctl enable httpd 
```
{% endcode %}

6\. Skip storage, skip tags

#### Security group&#x20;

Once you get to Configure Security group:

Create a new security group. Give it the name **webserver-sg**.&#x20;

* Add a rule in the security group that allows SSH connections from your IP address.&#x20;
* Add a rule that allows HTTP traffic from your IP address (port 80).&#x20;

Then review and launch.&#x20;

For key pairs, you can use cli-keys.pem.&#x20;

#### Name the instance&#x20;

Once the instance has been launched, give it a name: **web-a.**

### Second instance

Repeat the steps above for a second instance. Use the same AMI, same instance type, same security group (use existing group **webserver-sg**).&#x20;

The key difference: put your instance in the subnet **alb-public-c.**&#x20;

In the user data, paste the following:

```
#!/bin/bash 
yum update -y 
yum install httpd -y 
systemctl start httpd 
cd /var/www/html
echo "<h1>This is instance web-c</h1>" > index.html
systemctl enable httpd 
```

Name your instance **web-c**.&#x20;

End result:

![I wish I'd known you can choose your columns sooner.. ](<../../.gitbook/assets/image (54).png>)

Instances have been launched. Here is what we have done:

![Instances in their subnets](<../../.gitbook/assets/image (169).png>)

#### Test that web servers work&#x20;

Once your instances have launched, you can access their public IP's via your browser. They should return the following:

&#x20;

![Web-c ](<../../.gitbook/assets/image (81).png>)

## Create target groups&#x20;

Next we want to create target groups that we are going to put our webservers in.&#x20;

In the EC2 service, you will find Load balancers on the left side panel.&#x20;

1. Expand load balancers
2. Click target groups
3. Click create target group&#x20;
4. Target type: instances&#x20;
5. Target group name: **webserver-a**
6. Protocol and port: HTTP, 80
7. VPC: **alb-vpc**
8. Protocol version: HTTP1
9. Leave other settings as they are and click Next.&#x20;
10. On the next page select your instance **web-a**&#x20;
11. Ports for the selected instances: 80
12. Click the "include as pending below"&#x20;
13. Scroll down and click Save.

Your target group has now been created.&#x20;

Repeat steps 1-13 to create another target group called **webserver-c**, using **web-c** as the instance. &#x20;

## Create ALB

Here is where we finally get to create our ALB.

1. In the EC2 service, click Load balancers
2. Create load balancer
3. Pick ALB (read through the descriptions though, they are useful info)&#x20;
4. After you click create, expand the How Application Load Balancers work: it's good reading
5. For Load balancer name, use **webserver-alb**
6. Scheme: internet-facing
7. IP address type: IPv4
8. Under network mapping:
   1. VPC is **alb-vpc**
   2. Mappings: expand both AZ's. Select the **alb-public-a** and **alb-public-c** subnets in their AZ's.
9. When you get to Security groups, click the link "Create new security group"
   1. This opens a new tab and a dialoge to create a new SG
   2. Name: **alb-sg**
   3. Description: SG for internet-facing ALB
   4. VPC: select **alb-vpc**
   5. Inbound rules: Add rule. Allow HTTP from Source Anywhere IPv4.&#x20;
   6. Outbound rules: Add rule. Allow HTTP to Destination **webserver-sg** (the sec group of our instances)
   7. Create the security group.&#x20;
10. Go back to the tab where we are creating our ALB
11. You may have to refresh the security group drop down for it to show the **alb-sg**
12. If the default sg is already selected, remove it with the X&#x20;
13. Select **alb-sg** as security group
14. Under listeners and routing:
    1. For Listener HTTP:80 the default action is to forward to Target group **webserver-a**
15. Create load balancer.&#x20;

It will take some time for your ALB to be fully operational.&#x20;

### Test the default action

Our ALB should be up and running. You will find it in the EC2 service under Load balancers.&#x20;

When we create an ALB, we have to have a target group for the default action. The default action cannot be left empty. That is why we used the **webserver-a** target group when we created the ALB. The default action can be changed later.

You will find the DNS name of your ALB in EC2 > Load balancers > click on the ID of your ALB.&#x20;

Copy the DNS name and paste it into a browser.&#x20;

![Well look who it is](<../../.gitbook/assets/image (381).png>)

### Security side note&#x20;

When we first tested our instances, we used their public IP addresses to access them over the internet. Now when we used the DNS name of our ALB, our request went through the load balancer and we reached the web-a instance without its public IP.&#x20;

Let's go edit the security group **webserver-sg**.&#x20;

1. Remove the inbound rule that allows HTTP from your IP&#x20;
2. Add an inbound rule that allows HTTP from the security group alb-sg

Then try to access the web-a instance with its public IP. The browser just hangs. You are able to use the DNS name of the ALB to access web-a though.&#x20;

This is an example of how you can force all requests to come through the load balancer. You could even have instances with no public IP's, even instances that are in private subnets and have no internet connectivity, but are able to receive requests from the load balancer. Our instances in this lab have to have public IP's because we will need to SSH into them.&#x20;

## Path-based routing

We want to use path-based routing in our ALB. We therefore need&#x20;

* different directories in our web servers
* a rule in our ALB's listener that does the routing.&#x20;

### Create a directory

Let's start with instance **web-a**. You'll need its public IP. &#x20;

SSH into the instance and run these commands:

{% code title="Orders directory" %}
```
sudo su - 
cd /var/www/html
mkdir orders && cd orders
echo "<h1>This is where orders get processed</h1>" > index.html
```
{% endcode %}

There is now an "Orders" directory in the web-a instance. Next we want to tell the load balancer how to get there.&#x20;

### Edit ALB rules

In the EC2 service,

1. Go to Load balancers
2. Check the box next to your ALB&#x20;
3. Scroll down and click the tab Listeners
4. Check the box next to the HTTP : 80 listener
5. Click "view/edit rules"

![Editing the listener for HTTP : 80 ](<../../.gitbook/assets/image (335).png>)

### Create path-based routing rule

You should see this grey ribbon:

![Grey ribbon-thingy](<../../.gitbook/assets/image (32).png>)

When you click on the plus sign, a blue text "+ Insert rule" appears above the default action. Click on it.&#x20;

Click on +Add condition.&#x20;

IF Path... is orders

THEN Forward to... webserver-a.&#x20;

Click Save.&#x20;

&#x20;

![Path-based routing for /orders](<../../.gitbook/assets/image (116).png>)

### Test the path-based routing rule

In your browser, go to long-dns-name-of-alb/orders&#x20;

![It worked!](<../../.gitbook/assets/image (107).png>)

### Edit the default action of the ALB - optional

Every LB has a default action: this is what happens to all requests that do not match any other rules. We can try changing it.&#x20;

1. Go back to view/edit rules for your HTTP : 80 listener.&#x20;
2. In the ribbon, click the pen
3. Click the pen on the left hand side of the default action rule
4. The IF cannot be changed
5. THEN can be changed to for example
   1. Forward to...&#x20;
   2. Redirect to...
   3. Return fixed response with an HTTP status code

You could for example return HTTP 503 if someone tries accessing the root of the ALB's DNS name.

## Create more rules

This is where you get to have some fun!&#x20;

For either instance web-a and web-c,&#x20;

1. SSH into them and create more directories
   1. make a new directory under /var/www/html and put an index.html there
2. Create a rule in the ALB's HTTP listener for that directory
3. You can even play with different weights

![Some features of the ALB](<../../.gitbook/assets/image (348).png>)

You can test the weighted routing with cURL:

```
for X in `seq 25`; do curl -so -i /dev/null -w "" http://asdfasdf.amazonaws.com/surprise-me/; done

```

## Health check(s)&#x20;

I know I know - longest lab ever. Just one more thing, okay?&#x20;

### EC2 health check

In the EC2 instances section we have this:&#x20;

![EC2 health check](<../../.gitbook/assets/image (51).png>)

### ALB health check

In the target groups we have this:

![ALB health checks ](<../../.gitbook/assets/image (388).png>)

![](<../../.gitbook/assets/image (336).png>)

We are going to discuss health checks in detail later.

## Clean up

🎯 ALB's cost money, so let's be sure to delete it when we are done.

Terminate your EC2 instances.&#x20;

You can delete the security groups we made, alb-sg and webserver-sg. This is to show that you first have to delete any rules that reference other security groups before you can delete a group.&#x20;

Delete target groups.&#x20;

