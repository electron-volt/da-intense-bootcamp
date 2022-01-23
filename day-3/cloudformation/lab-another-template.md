# LAB: Another template

In this lab we want to expand on our first template.&#x20;

## Starting point

This is the template that we started with, along with comments that explain it line by line:

```yaml
Resources:                           # mandatory 
  TestInstance:                      # you could name this anything 
    Type: AWS::EC2::Instance         # this line states we want an EC2 instance
    Properties:
      ImageId: ami-06bfd6343550d4a29 # AMI to use 
      InstanceType: t3.micro         # instance type to use
```

## New template

Take a moment to read through this longer template:

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Parameters:
  KeyName:
    Description: Name of an existing EC2 KeyPair to enable SSH access to the instance
    Type: AWS::EC2::KeyPair::KeyName
    ConstraintDescription: must be the name of an existing EC2 KeyPair.
  InstanceType:
    Description: EC2 instance type
    Type: String
    Default: t3.micro
    AllowedValues: [t2.nano, t2.micro, t3.micro]
    ConstraintDescription: must be a valid EC2 instance type.
Mappings: 
  RegionMap: 
    eu-north-1: 
      "HVM64": "ami-06bfd6343550d4a29"
    eu-west-1: 
      "HVM64": "ami-04dd4500af104442f"
    eu-central-1:
      "HVM64": "ami-05d34d340fb1d89e5"
Resources:
  EC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref 'InstanceType'
      KeyName: !Ref 'KeyName'
      ImageId: !FindInMap [RegionMap, !Ref "AWS::Region", HVM64]
Outputs:
  InstanceId:
    Description: InstanceId of the newly created EC2 instance
    Value: !Ref 'EC2Instance'
  AZ:
    Description: Availability Zone of the newly created EC2 instance
    Value: !GetAtt [EC2Instance, AvailabilityZone]
  PublicIP:
    Description: Public IP address of the newly created EC2 instance
    Value: !GetAtt [EC2Instance, PublicIp]
```

### Parameters

When we create a stack with this template, we will be asked to provide&#x20;

* a stack name (that always happens, not specific to this template)
* a key name (with a dropdown of all key pairs in the region we are in)&#x20;
* an instance type (t3.micro will be selected, but the dropdown will also have two other options).

### Mappings

Here is where we make sure that the template will launch the Amazon Linux 2 AMI, independent of whether we are in Stockholm, Ireland or Frankfurt. (By the way notice how Stockholm and Frankfurt are cities, but Ireland is a nation-state? Why are some regions countries and some cities. I don't know.)&#x20;

### Resources

This template will launch only one AWS resource: an EC2 instance. It will have properties

* Instance type: selected by the user&#x20;
* Key pair: selected by the user
* Image id: determined by the region that the user is in.

### Outputs

The template will output

* The instance ID
* The AZ that the instance is in
* the public IP address.

Since we did not specify a VPC or a security group, the instance will be launched in some public subnet of the default VPC and will be associated with the default security group of the VPC.

### Save it

Save the template on your computer as labtemplate.yml.

## Ireland

Let's log in to the AWS console, but switch to the eu-west-1 region.&#x20;

### Key pair

We need to have a key pair in the Ireland region for this lab to work, so go ahead and create one.&#x20;

1. Go to EC2
2. Under Network & security click Key pair
3. Create key pair
4. Name it something memorable like irish-keys.

We won't be actually SSH'ing into an instance, so no need to do the whole chmod thing.&#x20;

### Create the stack&#x20;

Next - while still in the Ireland region - go to CloudFormation.&#x20;

Create a stack using the labtemplate.yml file. Note: as I write this, in the Ireland region it is **t2.micro** which is the free tier eligible instance type.&#x20;

![Creating the stack](<../../.gitbook/assets/image (202).png>)

Once the stack is in state CREATE COMPLETE, you can view the outputs:

![So cool](<../../.gitbook/assets/image (323).png>)

### Clean up&#x20;

Delete the stack. This will also delete the instance.&#x20;

NOTE: keep the key pair irish-keys. Key pairs don't cost anything and we will need a key pair in the Ireland region on day 4.&#x20;

## Via the CLI - optional

Here is a command that will create stack "cli-stack" using this labtemplate.yml with parameter values cli-keys and t3.micro. In my AWS profile I've set eu-north-1 as the default region.&#x20;

`aws cloudformation create-stack`&#x20;

`--stack-name cli-stack`&#x20;

`--template-body file:///labtemplate.yml`&#x20;

`--parameters ParameterKey=KeyName,ParameterValue=cli-keys ParameterKey=InstanceType,ParameterValue=t3.micro`

## &#x20;
