# ECS Service

## Create an ALB

Is creating an application load balancer like riding a bicycle? Once you learn it, it just comes back to you automatically from muscle memory? We'll see.&#x20;

### Create a load balancer

We want an ALB with these settings:

* name: ecs-lab-alb
* internet-facing
* uses IPv4

![my cropping sucks](<../../../../.gitbook/assets/image (375).png>)

### Networking

* VPC is the ECSLabVPC
* Mappings: check both AZ's, pick our PUBLIC subnets

![VPC settings](<../../../../.gitbook/assets/image (197).png>)

### Security group

* Security group: there is one with -ALBSG- in the name, choose that one
* remove the default security group

![SG](<../../../../.gitbook/assets/image (301).png>)

### Listeners and routing

Listener is HTTP : 80.&#x20;

For default action, pick **create target group**

#### Target group&#x20;

Target type: **Instances**

Target group name: **web**

Protocol HTTP : 80

VPC: the ECS one

Protocol version HTTP1

Health checks: leave defaults

Click **Next**.&#x20;

Do not register any targets.&#x20;

**Create target group**.&#x20;

#### Back to the ALB

Now back in the ALB creation dialogue, pick the web target group as the default action.&#x20;

If the dropdown is empty, then click the refresh circular arrow thing. There is no emoji for it!&#x20;

There apparently is an emoji for _everything_ else tho: ❖ ❦ ␖ ␆

![](<../../../../.gitbook/assets/image (85).png>)

Then create load balancer.&#x20;

![pretty much sums it up](<../../../../.gitbook/assets/image (262).png>)

## Deploy service "web"

Let's go to ECS and find our cluster. In the Services tab there is a blue button "Create" - click on it.&#x20;

![click the blue button please](<../../../../.gitbook/assets/image (310).png>)

### Configure service

Pick EC2, task def web, cluster ECS-LAB, service name web and number of tasks 2.

![](<../../../../.gitbook/assets/image (228).png>)

#### Deployment type

Rolling update

#### Task placement

AZ balanced spread

#### Tags

Enable ECS managed tags

### Configure network

Under Load balancing, pick Application Load Balancer.&#x20;

For role, you should find **AWSServiceRoleforECS** in the dropdown.

Use the **ecs-lab-alb** as ALB.

Container to load balance: make sure **web:0:80** is selected and click **Add to load balancer**.&#x20;

![ALB settings](<../../../../.gitbook/assets/image (38).png>)

### Container to load balance

This is simple. Pick **80:HTTP** for production listener port.&#x20;

Target group: pick **web**.&#x20;

![web : 80 settings](<../../../../.gitbook/assets/image (314).png>)

#### Auto scaling&#x20;

Do not adjust the service's desired count.&#x20;

#### Review and create service.&#x20;

![Service web has tasks pending. ](<../../../../.gitbook/assets/image (266).png>)

### View service

In the console you can now view your service. There are two tasks and they will after some time be  RUNNING.&#x20;

## Deploy service "cats"

The steps are almost the same as for web.&#x20;

![](<../../../../.gitbook/assets/image (254).png>)

Proceed with the same settings all the way till you get to load balancing.&#x20;

### Container to load balance

You should see this:

![](<../../../../.gitbook/assets/image (331).png>)

Click the "Add to load balancer". Settings are as follows:

![target group and listener settings](<../../../../.gitbook/assets/image (345).png>)

Click Next steps, do not use auto scaling. Next > review > create > view service.&#x20;

## Deploy service "dogs" - Fargate

For dogs service, the launch type is Fargate so steps will be slightly different.&#x20;

Go to ECS > your cluster > services > create.&#x20;

![dogs service](<../../../../.gitbook/assets/image (325).png>)

Use Fargate, os Linux. Name of service is dogs. Number of tasks is 2.&#x20;

Deployment type: rolling

Enable ECS managed tags.&#x20;

### Configure network

For the Fargate launch type, we have the awsvpc network mose, so we have to set up our network configuration differently than for the EC2 types.&#x20;

Cluster VPC: pick the one with CIDR that starts 10.0.0...

Pick both of our **PRIVATE** subnets.&#x20;

security group is created automatically for you.&#x20;

Auto assign public IP: **DISABLED**.&#x20;

![network settings](<../../../../.gitbook/assets/image (318).png>)

### Load balancing&#x20;

When you get to load balancing, **pick the Application load balancer.** You will see this:

![](<../../../../.gitbook/assets/image (162).png>)

Click on Add to load balancer.&#x20;

### Container to load balance

Here are the settings to use:

![](<../../../../.gitbook/assets/image (76).png>)

When you scroll down, you can see the existing paths used on the HTTP : 80 listener:

![existing paths ](<../../../../.gitbook/assets/image (134).png>)

Proceed as usual. Skip auto scaling. Create.

## View tasks

Here are the running tasks in our cluster now:

![running tasks](<../../../../.gitbook/assets/image (359).png>)

The EC2 based tasks have instances. The Fargate serverless tasks do not have instances.&#x20;

###

