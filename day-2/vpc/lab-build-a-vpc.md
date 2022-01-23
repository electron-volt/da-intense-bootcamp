# LAB: Build a VPC

We are going to build a VPC. It will have the following:

* A VPC with CIDR block 10.0.0.0/16
* Two subnets: one private with CIDR block 10.0.1.0/24 and one public with CIDR block 10.0.2.0/24
* An Internet gateway.

![Our VPC](<../../.gitbook/assets/image (16).png>)

## Create the VPC

Navigate to the VPC service. There is a big button that says "Launch VPC Wizard" - that is not what we want. It would hide the nitty gritty from us. It can be very useful later if you want to build something in AWS and want to have the networking set up on your behalf.&#x20;

Today we will take the long road and do things from scratch, to pay our dues.&#x20;

1. On the left hand side panel, go to Your VPCs.&#x20;
2. Click Create VPC
3. You can give your VPC a name (note: it's actually creating a tag). I will call this VPC **my-vpc.**
4. We want **IPv4 CIDR manual input** for our CIDR block&#x20;
5. IPV4 CIDR: **10.0.0.0/16**
6. No IPv6 block&#x20;
7. Tenancy: **default**&#x20;
8. Skip the tags&#x20;
9. Create VPC

## Create the subnets

Next we want two subnets.&#x20;

1. On the left hand side, go to Subnets.
2. Click Create subnet
3. From the drop down choose the newly created my-vpc
4. Under subnet settings, for the first subnet:
   1. Subnet name: private
   2. AZ: pick the one that ends in 1a
   3. CIDR block: 10.0.1.0/24
5. Click Add new subnet
6. Under subnet settings, for the second subnet:
   1. Subnet name: public
   2. AZ: pick the one that ends in 1b
   3. CIDR block: 10.0.2.0/24
7. Create subnets.&#x20;

We have our two subnets ready.&#x20;

## Create the Internet gateway

If we want our VPC to be able to communicate with the internet, we need to create an IGW. A VPC can only have one IGW and all internet traffic goes through it.&#x20;

1. On the left hand side panel, go to Internet gateways
2. There will be one listed, for the default VPC. Click Create internet gateway
3. Give it a name: my-vpc-igw
4. Create IGW&#x20;

This IGW needs to be attached to a VPC. You should see a green banner and a button on the right hand side that says Attach to VPC.&#x20;

Since our default VPC already has an IGW, there is only one VPC for you to attach this IGW to. Go ahead and select my-vpc from the dropdown and attach the IGW.&#x20;

#### Situation so far

We have a VPC with an IGW and two subnets. So we're done, right? Not quite.&#x20;

## Create Route tables

Route tables are what determine where traffic in your VPC goes. The real difference between a private and public subnet is that the public subnet's route table has a route to the IGW whereas the private one's doesn't.&#x20;

We therefore need two route tables:

* A route table for the public subnet with a route to the IGW
* A route table for the private subnet with just a "local" route.

Here's how we do it:

1. On the left hand side panel, go to Route Tables
2. You will find two route tables there.

Look at the VPC's these route tables are associated with: one is associated with **my-vpc** and the other with the default VPC. Select the one associated with **my-vpc**.&#x20;

The route table looks like this:

![Route table 1](<../../.gitbook/assets/image (102).png>)

This route table does not have a route to the IGW. It can only route traffic within the VPC, but not to the internet.

Next click on Subnet associations. There are no explicit subnet associations. Let's create one:  we are going to associate the private subnet with this route table. (This isn't strictly speaking necessary, as all subnets in the VPC are implicitly associated with the "main" route table.)

1. Create subnet association
2. Pick the private subnet
3. Create association.&#x20;

Let's head back to Route Tables. Click on the second one, the route table associated with the default VPC. You'll see this:

![Default VPC route table](<../../.gitbook/assets/image (207).png>)

This route table can route traffic within the default VPC and also out to the internet. The route 0.0.0.0/0 has a target that is the IGW of the default VPC.&#x20;

Our goal is to create a similar route table for our my-vpc: it will have a local route and a route to the internet.&#x20;

1. Let's go back to the Route Tables list and click Create route table.&#x20;
2. Name: public-rt
3. VPC: my-vpc
4. Create route table.&#x20;

Once it has been created, we can see the routes.&#x20;

1. Let's click Edit routes.&#x20;
2. Click Add route
3. For destination write 0.0.0.0/0&#x20;
4. Target: Internet gateway&#x20;
5. Then choose the my-vpc gateway&#x20;
6. Save changes.&#x20;

Your public-rt should now look like this:

![routes in public-rt](<../../.gitbook/assets/image (78).png>)

Next, associate the public subnet with this route table like we did before for the private one.&#x20;

You can also give the route table associated with the private subnet the name private-rt. Just to make them easier to differentiate, I also named the route table of the default VPC default-rt:

![route tables and their names](<../../.gitbook/assets/image (35).png>)

(I've named the default VPC in my AWS account Oletus).&#x20;

### End result

We have now successfully created a VPC that has two subnets: one with internet access and one without.&#x20;

![Our VPC](<../../.gitbook/assets/image (379).png>)
