# LAB: EC2 via the CLI

## Creating an EC2 instance with the CLI

We will repeat the steps from the previous lab with the CLI.&#x20;

### Create a key pair

We could use the same key pair that we just used, but just to get a little more practice with the CLI let's create a new one. Windows users, you do not have to do the whole Puttygen thing this time as we don't really need to SSH into the instance.&#x20;

We will create a key pair called cli-keys.pem with the following command:

`aws ec2 create-key-pair --key-name cli-keys --query 'KeyMaterial' --output text > cli-keys.pem`

You can check that the keys were created using&#x20;

`aws ec2 describe-key-pairs`

Make sure to run&#x20;

`chmod 400 cli-keys.pem`

Windows users again have some more hoops to jump through.

### Create a security group&#x20;

First we must find out what our VPC ID is. We will get to what VPC's are very soon. Run this:

`aws ec2 describe-vpcs`

In the output you will find "VpcId". Copy its value, it will be vpc-123abcsomething.

Next create the security group called cli-sg:&#x20;

`aws ec2 create-security-group --group-name cli-sg --description "My CLI group" --vpc-id vpc-123abc..`

As output, the id of the security group is returned. You will need it in the next phase.

#### Find your IP

Easy way to find your IP:

`curl https://checkip.amazonaws.com`

#### Add rules to the security group&#x20;

Allow SSH connections from your IP with this command:

`aws ec2 authorize-security-group-ingress --group-id <sec group id> --protocol tcp --port 22 --cidr <your ip>`

Add your IP in the format  x.x.x.x/32. There is no output if the command runs successfully.&#x20;

We won't be setting up a web server in this instance so you do not need to allow traffic to port 80.

### Launch an instance

First we will need the ID of the AMI that we used last time. Run the following:

`aws ec2 describe-instances`

This returns all the information about our running instances. From the output, we can pick out the ImageId: **ami-06bfd6343550d4a29** of the AMI that we used to launch the instance.&#x20;

We will launch a new instance with this command:

aws ec2 run-instances --image-id ami-06bfd6343550d4a29 --count 1 --instance-type t3.micro --key-name cli-keys --security-group-ids \<security group ID>&#x20;

The output will contain information about the recently launched instance. While it is still initializing, it won't have a public IP yet:

{% code title="Snippet of the output" %}
```
            "PrivateDnsName": "..... ",
            "PrivateIpAddress": "......",
            "ProductCodes": [],
            "PublicDnsName": "",
            "State": {
                "Code": 0,
                "Name": "pending"
            },
```
{% endcode %}

Make note of what the instance id of your new instance is. Wait for a little while. Then run&#x20;

`aws ec2 describe-instances --instance-ids <instance id of new instance>`&#x20;

The output now contains the following:

{% code title="snippet of the output" %}
```
                    "PrivateDnsName": ".....",
                    "PrivateIpAddress": ".....",
                    "ProductCodes": [],
                    "PublicDnsName": ".....",
                    "PublicIpAddress": "16.171.8.247",
                    "State": {
                        "Code": 16,
                        "Name": "running"
```
{% endcode %}

We now have the public IP of our instance.&#x20;

### Connect to the instance&#x20;

You could SSH into the instance the way we did before:

`ssh -i cli-keys.pem ec2-user@<public IP>`

You can skip this step as it doesn't really offer anything new.&#x20;

## Cleaning up&#x20;

While our EC2 instances are free tier eligible, we still  still delete them straight away.&#x20;

To delete instances via the CLI, run

```
aws ec2 terminate-instances --instance-ids <instance id> 
```

You can find the instance id's of your instances with&#x20;

`aws ec2 describe-instances`

OR&#x20;

Via the console, go to EC2 > instances, select both instances, then Instance state > terminate instance.&#x20;
