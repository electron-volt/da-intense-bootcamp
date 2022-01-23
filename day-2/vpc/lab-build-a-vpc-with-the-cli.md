# LAB: Build a VPC with the CLI

We will do the exact same thing as in the last lab, but this time using the CLI. We will have:

* A VPC with CIDR block 10.1.0.0/16
* Two subnets: one private with CIDR block 10.1.1.0/24 and one public with CIDR block 10.1.2.0/24
* An Internet gateway.

## Create the VPC

`aws ec2 create-vpc --cidr-block 10.1.0.0/16 --query Vpc.VpcId --output text`

This command returns your VPC ID. You'll need it in the next step.&#x20;

## Create the subnets

`aws ec2 create-subnet --vpc-id <vpc id> --cidr-block 10.1.1.0/24`

`aws ec2 create-subnet --vpc-id <vpc id> --cidr-block 10.1.2.0/24`

The output of these commands will include information such as the subnet ID. Make note of the subnet ID of the latter subnet with CIDR 10.1.2.0/24, as we will need it later.&#x20;

## Create the internet gateway

`aws ec2 create-internet-gateway --query InternetGateway.InternetGatewayId --output text`

This command returns the ID of the IGW.

Attach the IGW to your new VPC:

`aws ec2 attach-internet-gateway --vpc-id <vpc id> --internet-gateway-id <igw id>`&#x20;

## Create route tables

We will create the route table that we want to use with the public subnet, so the route table with a route to the IGW. The private subnet is implicitly associated with the main route table of the new VPC. Here is how we create a new route table:

`aws ec2 create-route-table --vpc-id <vpc id> --query RouteTable.RouteTableId --output text`

This command returns the id of the route table.&#x20;

Next we want to add a route to the IGW into this route table:

aws ec2 create-route --route-table-id \<rt id> --destination-cidr-block 0.0.0.0/0 --gateway-id \<igw id>

If it succeeded, the command will return `{"Return": true}.`

Now we want to associate the subnet with CIDR 10.1.2.0/24 with this new route table:

`aws ec2 associate-route-table --subnet-id <subnet id> --route-table-id <route table id>`

If it succeeded, it will return information like association id and state.

## End result&#x20;

We have created a VPC with private and public subnets via the CLI.&#x20;
