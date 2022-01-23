# VPC

Amazon Virtual Private Cloud (Amazon VPC) enables you to launch AWS resources into a virtual network that you've defined. This virtual network closely resembles a traditional network that you'd operate in your own data center, with the benefits of using the scalable infrastructure of AWS.

## Key concepts&#x20;

* **Virtual private cloud (VPC)** — A virtual network dedicated to your AWS account.
* **Subnet** — A range of IP addresses in your VPC. A subnet can be private (no internet access) or public (has internet access).&#x20;
* **Route table** — A set of rules, called routes, that are used to determine where network traffic is directed.&#x20;
* **Internet gateway** — A gateway that you attach to your VPC to enable communication between resources in your VPC and the internet.

What we mean by public subnet is really, a subnet that has a route to the IGW in its route table. Similarly a private subnet is a subnet that does not have a route to the IGW in its route table.&#x20;

## Default VPC

So how is it we have been able to launch EC2 instances complete with public and private IP addresses without knowing (or at least doing) anything about our network? This is because every AWS account comes with a default VPC that is ready to use. This makes it possible for new users to get started with for example EC2 instances without having to do any configuration.&#x20;

The default VPC has:

* an internet gateway &#x20;
* a VPC with size /16 IPv4 CIDR block 172.31.0.0/16
* size /20 default subnets in each AZ&#x20;

and the necessary route tables and such to get the thing working.&#x20;

Something to note about the default VPC: EC2 instances launched into subnets in the default VPC automatically get public IP addresses and public DNS names. If you create your own VPC and subnets, then new instances do not automatically receive public IP's. You would have to change a setting called **Enable auto-assign public IPv4 address** for these subnets.&#x20;

Next we will walk through setting up our own VPC step by step.&#x20;

