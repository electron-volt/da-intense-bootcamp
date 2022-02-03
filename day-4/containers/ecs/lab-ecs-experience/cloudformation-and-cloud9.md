# CloudFormation and Cloud9

## Destination: Ireland 🍀

First, let's switch to the Ireland region, eu-west-1. We will build our lab in this region.&#x20;

### IMPORTANT: VPC

If you have built custom VPC's in the Ireland region, please make sure that there is no overlap between their CIDR blocks and the following CIDR blocks:

```
    private-subnet-1: {CIDR: 10.0.3.0/24}
    private-subnet-2: {CIDR: 10.0.4.0/24}
    public-subnet-1: {CIDR: 10.0.1.0/24}
    public-subnet-2: {CIDR: 10.0.2.0/24}
    vpc: {CIDR: 10.0.0.0/16}
```

If networking and CIDR blocks are not completely familiar, then it is best to delete all custom VPC's in the Ireland region first. This way you should only have one VPC in Ireland at the start of this lab:

![default VPC in Ireland](<../../../../.gitbook/assets/image (216).png>)

The default VPC will have CIDR 172.31.0.0/16.

## CloudFormation stack

Here is a template that we are going to use to create a stack.&#x20;

1. Save the file on your computer as ecs-lab-template.yaml
2. Go to CloudFormation
3. Create stack
4. For template source, choose upload
5. Upload the ecs-lab-template.yaml
6. Recommended: View in Designer (open in different tab)

In Designer you can view all the resources that the template is going to provision. It's also good to read through the template.&#x20;

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: 'ECS lab'
Mappings:
  CidrMappings:
    private-subnet-1: {CIDR: 10.0.3.0/24}
    private-subnet-2: {CIDR: 10.0.4.0/24}
    public-subnet-1: {CIDR: 10.0.1.0/24}
    public-subnet-2: {CIDR: 10.0.2.0/24}
    vpc: {CIDR: 10.0.0.0/16}
  DomainNameMappings:
    eu-west-1: {Domain: eu-west-1.compute.internal}
Outputs:
  PrivateSubnet1:
    Description: The first private subnet.
    Value: {Ref: PrivateSubnet1}
  PrivateSubnet2:
    Description: The second private subnet.
    Value: {Ref: PrivateSubnet2}
  PublicSubnet1:
    Description: The first public subnet.
    Value: {Ref: PublicSubnet1}
  PublicSubnet2:
    Description: The second public subnet.
    Value: {Ref: PublicSubnet2}
  VPC:
    Description: The VPC Id.
    Value: {Ref: VPC}
  VpcCidr:
    Description: The CIDR block of the VPC.
    Value:
      Fn::FindInMap: [CidrMappings, vpc, CIDR]
Resources:
  AttachGateway:
    DependsOn: [VPC, InternetGateway]
    Properties:
      InternetGatewayId: {Ref: InternetGateway}
      VpcId: {Ref: VPC}
    Type: AWS::EC2::VPCGatewayAttachment
  DHCPOptions:
    Properties:
      DomainName:
        Fn::FindInMap:
        - DomainNameMappings
        - {Ref: 'AWS::Region'}
        - Domain
      DomainNameServers: [AmazonProvidedDNS]
    Type: AWS::EC2::DHCPOptions
  EIP:
    Properties: {Domain: vpc}
    Type: AWS::EC2::EIP
  InternetGateway: {DependsOn: VPC, Type: 'AWS::EC2::InternetGateway'}
  NAT:
    DependsOn: AttachGateway
    Properties:
      AllocationId:
        Fn::GetAtt: [EIP, AllocationId]
      SubnetId: {Ref: PublicSubnet1}
    Type: AWS::EC2::NatGateway
  PrivateRoute:
    DependsOn: [PrivateRouteTable, NAT]
    Properties:
      DestinationCidrBlock: 0.0.0.0/0
      NatGatewayId: {Ref: NAT}
      RouteTableId: {Ref: PrivateRouteTable}
    Type: AWS::EC2::Route
  PrivateRouteTable:
    DependsOn: [VPC, AttachGateway]
    Properties:
      Tags:
      - {Key: Name, Value: PrivateRouteTable}
      VpcId: {Ref: VPC}
    Type: AWS::EC2::RouteTable
  PrivateSubnet1:
    DependsOn: AttachGateway
    Properties:
      AvailabilityZone:
        Fn::Select:
        - '0'
        - {'Fn::GetAZs': ''}
      CidrBlock:
        Fn::FindInMap: [CidrMappings, private-subnet-1, CIDR]
      Tags:
      - {Key: Name, Value: PrivateSubnet1}
      VpcId: {Ref: VPC}
    Type: AWS::EC2::Subnet
  PrivateSubnet1RouteTableAssociation:
    DependsOn: [PrivateRouteTable, PrivateSubnet1]
    Properties:
      RouteTableId: {Ref: PrivateRouteTable}
      SubnetId: {Ref: PrivateSubnet1}
    Type: AWS::EC2::SubnetRouteTableAssociation
  PrivateSubnet2:
    DependsOn: AttachGateway
    Properties:
      AvailabilityZone:
        Fn::Select:
        - '1'
        - {'Fn::GetAZs': ''}
      CidrBlock:
        Fn::FindInMap: [CidrMappings, private-subnet-2, CIDR]
      Tags:
      - {Key: Name, Value: PrivateSubnet2}
      VpcId: {Ref: VPC}
    Type: AWS::EC2::Subnet
  PrivateSubnet2RouteTableAssociation:
    DependsOn: [PrivateRouteTable, PrivateSubnet2]
    Properties:
      RouteTableId: {Ref: PrivateRouteTable}
      SubnetId: {Ref: PrivateSubnet2}
    Type: AWS::EC2::SubnetRouteTableAssociation
  PublicRoute:
    DependsOn: [PublicRouteTable, AttachGateway]
    Properties:
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: {Ref: InternetGateway}
      RouteTableId: {Ref: PublicRouteTable}
    Type: AWS::EC2::Route
  PublicRouteTable:
    DependsOn: [VPC, AttachGateway]
    Properties:
      Tags:
      - {Key: Name, Value: PublicRouteTable}
      VpcId: {Ref: VPC}
    Type: AWS::EC2::RouteTable
  PublicSubnet1:
    DependsOn: AttachGateway
    Properties:
      AvailabilityZone:
        Fn::Select:
        - '0'
        - {'Fn::GetAZs': ''}
      CidrBlock:
        Fn::FindInMap: [CidrMappings, public-subnet-1, CIDR]
      Tags:
      - {Key: Name, Value: PublicSubnet1}
      VpcId: {Ref: VPC}
    Type: AWS::EC2::Subnet
  PublicSubnet1RouteTableAssociation:
    DependsOn: [PublicRouteTable, PublicSubnet1, AttachGateway]
    Properties:
      RouteTableId: {Ref: PublicRouteTable}
      SubnetId: {Ref: PublicSubnet1}
    Type: AWS::EC2::SubnetRouteTableAssociation
  PublicSubnet2:
    DependsOn: AttachGateway
    Properties:
      AvailabilityZone:
        Fn::Select:
        - '1'
        - {'Fn::GetAZs': ''}
      CidrBlock:
        Fn::FindInMap: [CidrMappings, public-subnet-2, CIDR]
      Tags:
      - {Key: Name, Value: PublicSubnet2}
      VpcId: {Ref: VPC}
    Type: AWS::EC2::Subnet
  PublicSubnet2RouteTableAssociation:
    DependsOn: [PublicRouteTable, PublicSubnet2, AttachGateway]
    Properties:
      RouteTableId: {Ref: PublicRouteTable}
      SubnetId: {Ref: PublicSubnet2}
    Type: AWS::EC2::SubnetRouteTableAssociation
  VPC:
    Properties:
      CidrBlock:
        Fn::FindInMap: [CidrMappings, vpc, CIDR]
      EnableDnsHostnames: 'true'
      EnableDnsSupport: 'true'
      Tags:
      - {Key: Name, Value: ECSLabVPC}
    Type: AWS::EC2::VPC
  VPCDHCPOptionsAssociation:
    Properties:
      DhcpOptionsId: {Ref: DHCPOptions}
      VpcId: {Ref: VPC}
    Type: AWS::EC2::VPCDHCPOptionsAssociation
  
  WorkstationSG:  
    Type: "AWS::EC2::SecurityGroup"
    Properties:
        GroupName: Workstation to Push images
        GroupDescription: EC2 SecurityGroup
        VpcId: {Ref: VPC}
        SecurityGroupIngress:
          - IpProtocol: tcp
            FromPort: '22'
            ToPort: '22'
            CidrIp: '0.0.0.0/0'
  ALBSG:
    Type: "AWS::EC2::SecurityGroup"
    Properties:
      GroupDescription: Enable HTTP to the load balancer
      VpcId: {Ref: VPC}
      SecurityGroupIngress:
      - IpProtocol: tcp
        FromPort: '80'
        ToPort: '80'
        CidrIp: '0.0.0.0/0'

  ECSInstanceSG:
    Type: "AWS::EC2::SecurityGroup"
    Properties:
      GroupDescription: Enable access to container instances, from the load balancer only
      VpcId: {Ref: VPC}
      SecurityGroupIngress:
      - SourceSecurityGroupId: {Ref: ALBSG} 
        IpProtocol: tcp
        FromPort: '0'
        ToPort: '65535'

  WorkstationRole:
    Type: AWS::IAM::Role
    Properties:
      Path: "/"
      ManagedPolicyArns:
        - "arn:aws:iam::aws:policy/AmazonEC2ContainerRegistryFullAccess"
        - "arn:aws:iam::aws:policy/AmazonS3FullAccess"
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service:
            - ec2.amazonaws.com
          Action:
            - sts:AssumeRole

  WorkstationProfile: 
         Type: AWS::IAM::InstanceProfile
         Properties: 
            Path: "/"
            Roles: 
            - Ref: "WorkstationRole"
  cloud9Environment:
    Properties:
      Name : ecsworkshop
      SubnetId: {Ref: PublicSubnet1} 
      Description: Use this to work with ECS cats & dogs workshop
      InstanceType: t2.micro
      Repositories:
        - RepositoryUrl: https://github.com/jimini55/catsdogs-cloud9
          PathComponent: ecsworkshop
    Type: AWS::Cloud9::EnvironmentEC2            
```

Once you have taken a look in Designer, then you can hit Next.&#x20;

Give the stack the name "ECS-lab-stack".

Keep clicking next until you get to review. There will be a section called Capabilities that asks you to confirm this: "**I acknowledge that AWS CloudFormation might create IAM resources."**&#x20;

Create stack.

Then just sit back and watch as CloudFormation does everything for you.&#x20;

### Result

You will have two stacks in CloudFormation:

* ECS-lab-stack
* aws-cloud9-ecsworkshop-\<gibberish>.

![Stacks are complete.](<../../../../.gitbook/assets/image (194).png>)

ECS-lab-stack creates the VPC. The second stack creates an EC2 instance that powers a Cloud9 IDE + a security group for the instance.&#x20;

Here is what the VPC looks like:

![Our environment](<../../../../.gitbook/assets/image (280).png>)

You can go take a look in the console and you will find the VPC, the subnets, the route tables, the IGW, Nat Gateway..

New to us, there is an Elastic IP address or static public IP. The EIP is used by the NAT Gateway. The NAT GW is providing internet connectivity into our private subnets.&#x20;

## Cloud9 IDE

Next we will configure our IDE.

### Assign IAM role to EC2 instance

Go to IAM > Roles. There you will find **ECS-lab-stack-WorkstationRole-\<random string>**. Check that it has the following policies:&#x20;

![policies for our role.](<../../../../.gitbook/assets/image (294).png>)

Then go to EC2 > instances. There should be one instance created by the CloudFormation stack.&#x20;

Select it, click actions > security > Modify IAM role. For IAM role, choose the WorkstationRole.&#x20;

![](<../../../../.gitbook/assets/image (71).png>)

The EC2 instance is now using an IAM role with the necessary permissions.&#x20;

Sidenote: you may notice the banner that acknowledges your success says "Successfully attached ECS-lab-stack-WorkstationProfile-... to instance i-0101". Hmm, profile? That is because what we have here is an **instance profile.** An IAM role used by an EC2 instance.&#x20;

### Credentials

Let's navigate over to Cloud9. Under your environments you should see the ecsworkshop IDE. Go ahead and open it.&#x20;

With the IDE open, on the upper right hand corner click on the gear icon ⚙️

Go to Preferences > AWS settings > credentials and disable AWS managed temp credentials.&#x20;

![hope the GUI doesn't change by the time you see this](<../../../../.gitbook/assets/image (25).png>)

#### Terminal&#x20;

You can close the Preferences tab. Take a look at the terminal: it already has a command that has been run: cloning this repo [https://github.com/jimini55/catsdogs-cloud9](https://github.com/jimini55/catsdogs-cloud9)

This magic is due to our CloudFormation template, starting on line 239.&#x20;

Next run this command in the terminal:&#x20;

```
rm -vf ${HOME}/.aws/credentials
```

This will remove all the AWS temp credentials that have been created by default. There is no output.&#x20;

#### Verify instance role

We want to make sure our instance is really using the correct IAM role. Test it out by running

```
aws sts get-caller-identity --query Arn 
```

Success looks like this:

`$ aws sts get-caller-identity --query Arn`&#x20;

`"arn:aws:sts::account-id:assumed-role/ECS-lab-stack-WorkstationRole-K373Bexample/i-example1234135"`

Next we want to update and install CLI tools, so run these commands

```
sudo pip install --upgrade awscli
```

```
sudo yum install -y jq
```

#### Region

This command sets your current region (Ireland) as the default region:

```
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
echo "export AWS_REGION=${AWS_REGION}" | tee -a ~/.bash_profile
aws configure set default.region ${AWS_REGION}
```

You can verify that eu-west-1 is now the default region:

```
aws configure get default.region
```

