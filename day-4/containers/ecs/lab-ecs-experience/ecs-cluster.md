# ECS Cluster

## ECS Cluster

Navigate to ECS. You will be greeted with an introductory screen if this is your first visit. Do **not** click "get started"!

On the left side panel, select Clusters > create cluster.&#x20;

For Cluster template, select **EC2 Linux + Networking**.&#x20;

Give your cluster the name **ECS-LAB**.

### Instance

Provisioning model: on-demand instance

EC2 instance type: m5d.large (hourly rate is $0.126 so.. not that pricey)

Numer of instances: 2

EC2 AMI: pick Amazon Linux 2

Root EBS volume: 30&#x20;

Key pair: irish-keys (created on day 3)

### &#x20;Networking

VPC: pick the **ECSLabVPC**

Subnets: choose both **PRIVATE** subnets 1 and 2

Auto assign public IP: use subnet setting

Security group: the one that starts **ECS-lab-stack-ECSInstanceSG (+random)**

### **IAM role**

Choose **create new role.**&#x20;

Nore: _if_ you have used ECS before, then your account will already have ECSInstanceRole. In that case, use that role. If such a role does not yet exist, it will get created automatically.&#x20;

### **Container insights**

Check enable container insights.&#x20;

### Create

We are finally ready to hit create! Wait for it to complete and then click View cluster.&#x20;

The cluster is right now creating two EC2 instances with type m5d.large.

## Cluster IAM role

Navigate to IAM > Roles. Find the role called **ECSInstanceRole**.

Add the **CloudWatchLogsFullAccess** policy to the role:

1. Attach policy
2. Filter for **CloudWatchLogsFullAccess**
3. Attach policy

![You did it!](<../../../../.gitbook/assets/image (298).png>)

### Status

Our cluster is up and ACTIVE, but it doesn't have any running tasks.&#x20;

![Our cluster ](<../../../../.gitbook/assets/image (176).png>)

You can see the two instances in the EC2 console (names end with -ECS-LAB):

![EC2 instances.](<../../../../.gitbook/assets/image (329).png>)
