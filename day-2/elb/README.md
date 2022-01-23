# ELB

AWS have this to say about their load balancing solutions:

* Elastic Load Balancing automatically distributes your incoming traffic across multiple targets, such as EC2 instances, containers, and IP addresses, in one or more Availability Zones.&#x20;
* It monitors the health of its registered targets, and routes traffic only to the healthy targets.
* Elastic Load Balancing scales your load balancer as your incoming traffic changes over time. It can automatically scale to the vast majority of workloads.

We will examine two different kinds of load balancers:

* Application Load Balancer or ALB&#x20;
* Network Load Balancer or NLB

There is also a Gateway Load Balancer and a legacy Classic Load Balancer, but we can ignore those.&#x20;

## Load balancer concepts

Here are the key concepts related to load balancers.&#x20;

### Load balancer

A _load balancer_ serves as the single point of contact for clients. The load balancer distributes incoming traffic across multiple targets, such as Amazon EC2 instances. This increases the availability of your application.&#x20;

### Listener

A _listener_ checks for connection requests from clients, using the protocol and port that you configure, and forwards requests to a target group.

### Target group&#x20;

Each _target group_ routes requests to one or more registered targets. Targets can be for example EC2 instances.

![Load balancing in AWS ](<../../.gitbook/assets/image (232).png>)

## NLB and ALB

### NLB

* A Network Load Balancer functions at the fourth layer of the Open Systems Interconnection (OSI) model.&#x20;
* It can handle millions of requests per second.&#x20;
* When you think NLB, think high performance and TCP/UDP.

### ALB

* An Application Load Balancer functions at the application layer, the seventh layer of the Open Systems Interconnection (OSI) model.&#x20;
* ALB can route requests based on the content: host, path, HTTP headers and so on
* When you think ALB, think layer 7 and more flexible routing options.
