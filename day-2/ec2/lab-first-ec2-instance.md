# LAB: First EC2 instance

## Creating our first EC2 instance

We will create our first ever EC2 instance! We will be using exclusively Linux instances, sorry Bill G.&#x20;

1. Navigate to EC2 > instances
2. Click Launch instances

You will be taken through all the steps required to launch your first instance.&#x20;

### Choose an AMI&#x20;

We will be using Amazon Linux. Look for this symbol&#x20;

![Free tier eligible Amazon Linux](<../../.gitbook/assets/image (123).png>)

If there are several kernel versions to choose from, pick the most recent (Kernel 5.10 as I write this)&#x20;

Make sure 64-bit (x86) is chosen and hit Select.&#x20;

### Choose an instance type

We want to keep our costs down, so look for an instance type that is free-tier eligible. This might change based on your region, but look for this:

![Free tier eligible instance type](<../../.gitbook/assets/image (362).png>)

The t3.micro has 2 virtual CPUs (vCPU) and 1 GiB of memory. Quite a modest instance type, suitable for testing and development. Feel free to scroll around to see how much vCPU and memory the other instance types offer.&#x20;

Once you've had a look, click the grey Configure Instance details button.&#x20;

### Configure instance details

Scroll all the way down to Advanced details. Expand.&#x20;

Leave all other settings as they are, but in the text area User data paste the following:

```
#!/bin/bash
yum update -y
yum install httpd -y
systemctl start httpd
systemctl enable httpd
cd /var/www/html
echo "This is a test page running on my first EC2 instance." > index.html
```

We could of course launch our instance, SSH into it and then run the above commands. However we are able to achieve the same end result - a web server that displays our message - with this user data script.&#x20;

Then hit Next: Add storage.

### Add storage

This is where we can add additional storage to our instances. Notice that the instance we are about to launch has a root volume - every instance has a root device volume or boot volume. Ours has 8 GiB.

![root volume for an EC2 instance](<../../.gitbook/assets/image (137).png>)

It has been set to be deleted on termination. This is the default behaviour.&#x20;

Let's leave all of these settings as they are. Hit Next: Add tags.&#x20;

### Tags - optional&#x20;

We have not discussed tags yet as we haven't needed them for anything. Tags are key-value-pairs that can help you identify, group and track your resources.&#x20;

We could for example tag all the resources we create today with the following tag

![Tag for day: 2](<../../.gitbook/assets/Screenshot 2022-01-02 at 14.28.18.png>)

and then at the end of the day we would have an easier time of deleting all resources for day 2, because we could find and identify them based on the tag. Tags have no other effect on the function of AWS services, they are only for your convenience.&#x20;

Go ahead and hit Next: Configure Security Group. &#x20;

### Security groups

Security groups are instance level firewalls (as opposed to network access control lists which we will look at later, which are subnet level firewalls). They are stateful.

Security groups by default block all incoming traffic. Any connection that you want to open up you must explicitly allow in the security group rules.&#x20;

We are going to create a security group that we can reuse in later labs. It will allow SSH (TCP port 22) and HTTP (TCP port 80) from your machine's IP address.&#x20;

1. Make sure "create a **new** security group" is selected&#x20;
2. Give it the name ssh-and-http
3. Description is optional&#x20;

You may find a rule allowing SSH is already present. Just go to **source** and from the dropdown choose **My IP**. AWS will automatically put your IPv4 address in. Description is optional.

![Using your IP address as source](<../../.gitbook/assets/image (179).png>)

Go ahead and add another rule to allow HTTP. What happens if I select anywhere for source?&#x20;

![Rule allowing HTTP from anywhere.](<../../.gitbook/assets/image (121).png>)

Notice how the source is 0.0.0.0/0, ::/0? This means all IPv4 (and IPv6) addresses would be able to access your web server. That is not what we want for this lab, so let's also change the source to **My IP**.&#x20;

Once you've made those changes, click **Review and Launch**.&#x20;

#### Launch&#x20;

You are able to review and edit details before you launch. We are happy with what we have, so let's go ahead and Launch our instance!&#x20;

### Key pair

If we want to be able to SSH into our instance, we need to create a key pair. When you press launch, a dialogue appears.&#x20;

1. Select "create a new key pair"
2. Key pair type is RSA
3. Give it the name "day2labs"
4. Download key pair.&#x20;
5. Launch instance.&#x20;

## Connecting to the instance

Your EC2 instance is ready to be SSH'd into when you see this:

![EC2 instance is running](<../../.gitbook/assets/image (291).png>)

You can give the instance a descriptive name, like Lab-EC2. Click on the blue instance id to view more information about the instance.&#x20;

Once you find the **public IPv4 address** of your instance, open it in your browser. You should see:

![It works!](<../../.gitbook/assets/image (87).png>)

Now let's SSH into our instance and maybe change the text to something else.&#x20;

### Windows users using PuTTY

Instructions are here: [https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html)

### MacOS or Linux

1. Open a terminal window
2. Change to the directory where your key pair (day2labs.pem) file is&#x20;
3. Run `chmod 400 day2labs.pem`
4. Then connect to your instance with `ssh -i day2labs.pem ec2-user@<public-ipv4>`
5. Type yes and hit enter when asked to continue connecting.

Once you are in, run this command:

`sudo yum update -y`

This updates packages on the system**.**

![if you know you know](<../../.gitbook/assets/image (198).png>)

### Customize your own instance!

By changing the text in /var/www/html/index.html, you can change what your web server is serving up to the world.&#x20;

![New text](<../../.gitbook/assets/image (206).png>)

Maybe do a little \<marquee> to make it feel like it's 1998 again.&#x20;

## A word about IP addresses

There are three different kinds of IP addresses that an EC2 instance can have:

#### Private IP&#x20;

Every instance has a private IP address.&#x20;

#### Dynamic public IP&#x20;

This is what we used to SSH into the instance.&#x20;

#### Static public IP

These are called Elastic IP's or EIP's in AWS. More about these later.&#x20;

For now let's look at private and dynamic public IP's and how they behave if the instance is stopped and then started or rebooted.

### Reboot

Let's try rebooting our instance. Go to the EC2 console in AWS.

#### IP addresses before reboot

First make note of what the private and public IP's of your instance are. Mine were

![IP's before reboot](<../../.gitbook/assets/image (97).png>)

Click instance state > reboot.

![Reboot instance](<../../.gitbook/assets/image (39).png>)

You will be asked to confirm this.&#x20;

Once your instance has successully rebooted, check the IP's again:

&#x20;

![IP's after reboot.](<../../.gitbook/assets/image (163).png>)

Looks like both have stayed the same.&#x20;

### Stop and start

Let's now try stopping our instance and then restarting it. The IP's are still the same as after the reboot.&#x20;

Go to instance state > stop instance. Confirm.&#x20;

Once your instance is in state "Stopped", go back to instance state > start instance.&#x20;

Click the refresh symbol next to Connect if the screen doesn't refresh automatically. What has happened to the IP's?&#x20;

![IP's after stop and start. ](<../../.gitbook/assets/image (186).png>)

Looks like our dynamic public IP has changed. The private one stays the same.&#x20;

If we wanted a public IP address that can remain unchanged even when instances are stopped and restarted, we would need an elastic IP.&#x20;

Let's leave the instance up and running and go create one via the CLI.&#x20;

