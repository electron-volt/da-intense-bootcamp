# Optional LAB: EBS volumes

## Create and attach an EBS volume&#x20;

Let's go to the EC2 service in AWS. First let's check which availability zone our EC2 instance is in. **Our EBS volume has to be created in the same AZ**. You will find this information under Instances.&#x20;

On the left hand side panel you will see Elastic Block Store, click on volumes. You will see one volume listed: it's the root volume of your running EC2 instance.&#x20;

### Create the volume

1. Click on Create volume
2. Choose volume type Cold HDD (sc1)
3. Pick a size. Minimum is 125 GB.&#x20;
4. For AZ pick the same one that your EC2 instance is in.&#x20;
5. For snapshot ID, choose "Don't create volume from a snapshot"
6. No encryption&#x20;

Your volume should now be ready to use.&#x20;

### Attach the volume&#x20;

Next we want to attach our newly created volume to our instance.&#x20;

1. While still in the list of volumes, check the checkbox next to the cold HDD&#x20;
2. Under Actions, choose Attach volume
3. Since we have only one instance running, it is easy to choose from the dropdown. If we had more instances, we'd need to check the instance ID (i-abc123 gibberish).&#x20;
4. AWS will suggest a name, probably /dev/sd\[f-p]
5. Click Attach volume

A green banner appears telling you the volume was successfully attached.&#x20;

Note that you are able to modify EBS volumes. You can for example go change the volume type or size of either one of your volumes, even when the attached EC2 instance is up and running. &#x20;

### (Optional) Mount the volume

You can mount the volume if you like. Not difficult but a little time consuming.&#x20;

[https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-using-volumes.html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-using-volumes.html)&#x20;

### Detach the volume

Note: if you mounted your volume, unmount it first with `$ umount -d /dev/sd?.`

1. In the EC2 console, go to EBS.&#x20;
2. Select your Cold HDD volume
3. Click Actions > detach volume
4. Confirm detach.

### Delete the volume

You guessed it! Select the volume, actions, delete.. if the delete option appears greyed out, refresh and try again.&#x20;

