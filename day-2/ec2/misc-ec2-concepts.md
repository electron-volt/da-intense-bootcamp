# Misc EC2 concepts

Some concepts as quick "one-liners":

#### **Instance profile**

The optimal way to grant your instances the IAM permissions they need: attach an IAM role to the instance.&#x20;

#### Elastic Network Interface

An _elastic network interface_ is a logical networking component in a VPC that represents a virtual network card. Each instance has a default network interface, called the _primary network interface_. You cannot detach a primary network interface from an instance. You can create and attach additional network interfaces with public/private IPv4/v6 addresses and so on. &#x20;

#### Placement group

You can use _placement groups_ to influence the placement of a group of _interdependent_ instances to meet the needs of your workload. Group options are cluster (for low latency), spread (to reduce correlated failures) and partition (for large distributed and replicated workloads).&#x20;

#### On-demand instance

With On-Demand Instances, you pay for compute capacity by the second with no long-term commitments. You have full control over its lifecycle—you decide when to launch, stop, hibernate, start, reboot, or terminate it. This is what we have been using so far.&#x20;

#### Spot instance&#x20;

Buy unused compute capacity from AWS at very low prices. AWS might need the capacity back so your instances may be terminated: only use spot for workloads that can be interrupted. Instances are well-suited for data analysis, batch jobs, background processing, and optional tasks.

#### Reserved instances and Savings Plans

If you know you will be running EC2 instances for years, then you can save money by reserving instances or using AWS Savings Plans.

#### &#x20;
