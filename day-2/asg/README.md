---
description: Yet another acronym to memorize, great.
---

# ASG

## Scaling

When an EC2 instance requires more capacity, there are two ways that you can scale it: make it bigger or make more of them.

![Scaling up vs out](<../../.gitbook/assets/image (142).png>)

When you scale up, you change the instance type to a more powerful one.&#x20;

When you scale out, you create more instances of the same size.

## Auto-scaling

With AWS Auto-scaling, you can have AWS automatically scale your instances out and in as needed. Your applications will always have just as much or as little computing power as is needed.&#x20;

### Concepts

#### Groups

Your EC2 instances are organized into _groups_ so that they can be treated as a logical unit for the purposes of scaling and management. When you create a group, you can specify its minimum, maximum, and, desired number of EC2 instances.

#### Configuration templates

Your group uses a _launch template_, or a _launch configuration_ (legacy), as a configuration template for its EC2 instances. You can specify information such as the AMI ID, instance type, key pair, security groups, and block device mapping for your instances.

#### Scaling options

Amazon EC2 Auto Scaling provides several ways for you to scale your Auto Scaling groups. For example, you can configure a group to scale based on the occurrence of specified conditions (dynamic scaling) or on a schedule.
