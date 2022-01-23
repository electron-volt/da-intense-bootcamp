---
description: All the different parts of a CF template.
---

# Template anatomy

## Template sections

### Format version

The `AWSTemplateFormatVersion` section (optional) identifies the capabilities of the template. The latest template format version is `2010-09-09` and is currently the only valid value.

```yaml
AWSTemplateFormatVersion: "2010-09-09"
```

### Description&#x20;

The `Description` section (optional) enables you to include comments about your template.

```yaml
Description: > 
  Here are some 
  details about 
  the template.
```

### Parameters

Use the optional `Parameters` section to customize your templates. Parameters enable you to input custom values to your template each time you create or update a stack.

```yaml
Parameters: 
  InstanceTypeParameter: 
    Type: String 
    Default: t2.micro 
    AllowedValues: 
      - t2.micro 
      - m1.small 
      - m1.large 
    Description: Enter t2.micro, m1.small, or m1.large. Default is t2.micro.
```

### Rules

The optional `Rules` section validates a parameter or a combination of parameters passed to a template during a stack creation or stack update. To use template rules, explicitly declare `Rules` in your template followed by an assertion. Use the rules section to validate parameter values before creating or updating resources.

Each template rule consists of two properties:

* _Rule condition_ (optional) — determines when a rule takes effect.
* _Assertions_ (required) — describes what values users can specify for a particular parameter.

In the example below the rules are used to force the EC2 instance types of different environments.

{% code title="Example rule" %}
```yaml
Rules:
  testInstanceType:
    RuleCondition: !Equals 
      - !Ref Environment
      - test
    Assertions:
      - Assert:
          'Fn::Contains':
            - - a1.medium
            - !Ref InstanceType
        AssertDescription: 'For a test environment, the instance type must be a1.medium'
  prodInstanceType:
    RuleCondition: !Equals 
      - !Ref Environment
      - prod
    Assertions:
      - Assert:
          'Fn::Contains':
            - - a1.large
            - !Ref InstanceType
        AssertDescription: 'For a production environment, the instance type must be a1.large'
```
{% endcode %}

### Mappings

The optional `Mappings` section matches a key to a corresponding set of named values. For example, if you want to set values based on a region, you can create a mapping that uses the region name as a key and contains the values you want to specify for each specific region. You use the `Fn::FindInMap` intrinsic function to retrieve values in a map.

In the example below there are different AMI ID's in different regions and architectures. Starting on line 12 the template is creating an EC2 instance. The correct AMI ID is chosen based on the region.

(Don't worry if the example seems a little complicated)&#x20;

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Mappings: 
  RegionMap: 
    us-east-1: 
      "HVM64": "ami-0ff8a91507f77f867"
      "HVMG2": "ami-0a584ac55a7631c0c" 
    us-west-1: 
      "HVM64": "ami-0bdb828fd58c52235"
      "HVMG2": "ami-066ee5fd4a9ef77f1" 
    eu-west-1: 
      "HVM64": "ami-047bb4163c506cd98" 
      "HVMG2": "ami-0a7c483d527806435"
Resources: 
  myEC2Instance: 
    Type: "AWS::EC2::Instance"
    Properties: 
      ImageId: !FindInMap [RegionMap, !Ref "AWS::Region", HVM64]
      InstanceType: m1.small
```

### Conditions

The optional [`Conditions`](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-condition.html) section contains statements that define the circumstances under which entities are created or configured. For example, you can create a condition and then associate it with a resource or output so that AWS CloudFormation only creates the resource or output if the condition is true. Similarly, you can associate the condition with a property so that AWS CloudFormation only sets the property to a specific value if the condition is true. If the condition is false, AWS CloudFormation sets the property to a different value that you specify.

In the example below production resources will only be created if the EnvType is equal to "prod".

```yaml
Conditions: 
  CreateProdResources: !Equals
    - !Ref EnvType
    - prod
```

### Resources - mandatory

The required `Resources` section declares the AWS resources that you want to include in the stack. We saw this in action in the previous lab:

```yaml
Resources:
  TestInstance:
    Type: AWS::EC2::Instance
    Properties:
      AvailabilityZone: eu-north-1a
      ImageId: ami-06bfd6343550d4a29
      InstanceType: t3.micro
```

### Outputs

The optional `Outputs` section declares output values that you can import into other stacks (to create cross-stack references), return in response (to describe stack calls), or view on the AWS CloudFormation console. For example, you can output the DNS name of a load balancer or the instance id of an EC2 instance.&#x20;

```yaml
Outputs: 
  BackupLoadBalancerDNSName: 
    Description: The DNSName of the backup load balancer 
    Value: !GetAtt BackupLoadBalancer.DNSName 
    Condition: CreateProdResources 
  InstanceID: 
    Description: The Instance ID 
    Value: !Ref EC2Instance
```
