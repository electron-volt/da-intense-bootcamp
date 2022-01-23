---
description: So what if you do want servers after all?
---

# EC2

Amazon EC2 stands for Elastic Compute Cloud. Or Elastic Cloud Compute. I can never remember which. Anyway, it's IaaS.

Amazon EC2 provides the following features:

* Virtual computing environments, known as _instances_
* Preconfigured templates for your instances, known as _Amazon Machine Images (AMIs)_, that package the bits you need for your server (including the operating system and additional software)
* Various configurations of CPU, memory, storage, and networking capacity for your instances, known as _instance types_
* Secure login information for your instances using _key pairs_ (AWS stores the public key, and you store the private key in a secure place)
* Storage volumes for temporary data that's deleted when you stop, hibernate, or terminate your instance, known as _instance store volumes_
* Persistent storage volumes for your data using Amazon Elastic Block Store (Amazon EBS), known as _Amazon EBS volumes_
* A firewall that enables you to specify the protocols, ports, and source IP ranges that can reach your instances using _security groups_
* Static IPv4 addresses for dynamic cloud computing, known as _Elastic IP addresses_
* Metadata, known as _tags_, that you can create and assign to your Amazon EC2 resources
* Virtual networks you can create that are logically isolated from the rest of the AWS Cloud, and that you can optionally connect to your own network, known as _virtual private clouds_ (VPCs)
