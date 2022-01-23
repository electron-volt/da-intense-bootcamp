# EC2 Concepts

**Instance**: An instance is a virtual server in the cloud. Its configuration at launch is a copy of the AMI that you specified when you launched the instance.

**AMI**: a machine image that determines what kind of instance gets launched. There are public AMI's and you can create your own.&#x20;

**Storage**: every EC2 instance has a root device volume. You can also attach more EBS volumes to your instance. Storage can be ephemeral (instance store) or persistent (EBS stores).&#x20;

**Security group**: a stateful instance-level firewall. If you want to say, SSH into your instance, you have to have a rule in the security group of that instance that explicitly allows connections from your IP address to port 22.&#x20;

**Instance type:** there are different types of instances and they are billed differently. They break down into different families for compute- or memory-optimized, general purpose instances and so on. Common cheap instance type is t2.micro or t3.micro.&#x20;

Enough with the theory - let's spin up an instance already!&#x20;
