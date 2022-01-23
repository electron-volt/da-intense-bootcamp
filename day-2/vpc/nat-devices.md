# NAT devices

Instances in private subnets do not have a route out to the internet, as we have seen. Those instances might still need occasional internet access, for example to get updates. NAT devices solve this problem.&#x20;

You can use a NAT device to allow instances in private subnets to connect to the internet, other VPCs, or on-premises networks. These instances can communicate with services outside the VPC, but they cannot receive unsolicited connection requests.

## NAT instance

You can create your own AMI that provides network address translation and use your AMI to launch an EC2 instance as a NAT instance. You launch a NAT instance in a public subnet to enable instances in the private subnet to initiate outbound IPv4 traffic to the internet or other AWS services, but prevent the instances from receiving inbound traffic initiated on the internet.

Shortcomings of NAT instances:

* They can become a bottleneck depending on the instance type&#x20;
* There is no in-built high availability or fault tolerance
* Maintenance is up to you&#x20;

## NAT gateway

AWS offer NAT gateways as an alternative to NAT instances. NAT gateways are more expensive, but they have the following benefits:

* They are highly available
* They can scale up to 45 Gbps
* They are managed by AWS.

