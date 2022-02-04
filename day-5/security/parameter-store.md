# Parameter Store

This will be a useful all-purpose lab that combines

* Parameter store
* Lambda
* AWS SAM templates
* X-Ray (which we have not discussed yet).

### 🎯 Region: us-east-1

Note: In this lab, we want to work in the **us-east-1** region.&#x20;

## CloudFormation stack from SAM template

### The resources&#x20;

Here are the resources that the stack will create:

![resources](<../../.gitbook/assets/image (283).png>)

* A Lambda function&#x20;
* A Parameter Store parameter
* an IAM role
* a KMS key
* an alias for the key.&#x20;

### The SAM template

This is what the template looks like. Copy the template and save it locally as ps-template.yml

{% code title="ps-template.yml" %}
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Integrating Lambda with Parameter Store
Resources:
  ParameterStoreBlogDevEncryptionKey:
    Properties:
      Description: Encryption key for secret config values for the Parameter Store
        Blog post
      EnableKeyRotation: false
      Enabled: true
      KeyPolicy:
        Id: key-default-1
        Statement:
        - Action:
          - kms:Create*
          - kms:Encrypt
          - kms:Describe*
          - kms:Enable*
          - kms:List*
          - kms:Put*
          - kms:Update*
          - kms:Revoke*
          - kms:Disable*
          - kms:Get*
          - kms:Delete*
          - kms:ScheduleKeyDeletion
          - kms:CancelKeyDeletion
          Effect: Allow
          Principal:
            AWS:
            - '*'
          Resource: '*'
          Sid: Allow administration of the key & encryption of new values
        - Action:
          - kms:Encrypt
          - kms:Decrypt
          - kms:ReEncrypt*
          - kms:GenerateDataKey*
          - kms:DescribeKey
          Effect: Allow
          Principal:
            AWS:
              Fn::GetAtt:
              - ParameterStoreBlogFunctionRoleDev
              - Arn
          Resource: '*'
          Sid: Allow use of the key
        Version: '2012-10-17'
    Type: AWS::KMS::Key
  ParameterStoreBlogDevEncryptionKeyAlias:
    Properties:
      AliasName: alias/ParameterStoreBlogKeyDev
      TargetKeyId:
        Ref: ParameterStoreBlogDevEncryptionKey
    Type: AWS::KMS::Alias
  ParameterStoreBlogFunctionDev:
    Properties:
      CodeUri: s3://computeblog-us-east-1/lambda-parameter-store/lambda-parameter-store-example.zip
      Description: Integrating lambda with Parameter Store
      Environment:
        Variables:
          APP_CONFIG_PATH: parameterStoreBlog
          AWS_XRAY_TRACING_NAME: ParameterStoreBlogFunctionDev
          ENV: dev
      FunctionName: ParameterStoreBlogFunctionDev
      Handler: lambda_function.lambda_handler
      Role:
        Fn::GetAtt:
        - ParameterStoreBlogFunctionRoleDev
        - Arn
      Runtime: python3.6
      Timeout: 5
      Tracing: Active
    Type: AWS::Serverless::Function
  ParameterStoreBlogFunctionRoleDev:
    Properties:
      AssumeRolePolicyDocument:
        Statement:
        - Action:
          - sts:AssumeRole
          Effect: Allow
          Principal:
            Service:
            - lambda.amazonaws.com
        Version: '2012-10-17'
      Policies:
      - PolicyDocument:
          Statement:
          - Action:
            - ssm:GetParameter*
            Effect: Allow
            Resource:
              Fn::Sub: arn:aws:ssm:${AWS::Region}:${AWS::AccountId}:parameter/dev/parameterStoreBlog*
          Version: '2012-10-17'
        PolicyName: ParameterStoreBlogDevParameterAccess
      - PolicyDocument:
          Statement:
          - Action:
            - logs:CreateLogGroup
            - logs:CreateLogStream
            - logs:PutLogEvents
            Effect: Allow
            Resource: '*'
          Version: '2012-10-17'
        PolicyName: ParameterStoreBlogDevLambdaBasicExecution
      - PolicyDocument:
          Statement:
          - Action:
            - xray:PutTraceSegments
            - xray:PutTelemetryRecords
            Effect: Allow
            Resource: '*'
          Version: '2012-10-17'
        PolicyName: ParameterStoreBlogDevXRayAccess
    Type: AWS::IAM::Role
  SimpleParameter:
    Properties:
      Description: Sample dev config values for my app
      Name: /dev/parameterStoreBlog/appConfig
      Type: String
      Value: '{"key1": "value1","key2": "value2","key3": "value3"}'
    Type: AWS::SSM::Parameter
Transform: AWS::Serverless-2016-10-31
```
{% endcode %}

### The Lambda function&#x20;

Take a moment to read through the Lambda function code (this is included in the template, **you do not need to copy this):**

```python
import os, traceback, json, configparser, boto3
from aws_xray_sdk.core import patch_all
patch_all()

# Initialize boto3 client at global scope for connection reuse
client = boto3.client('ssm')
env = os.environ['ENV']
app_config_path = os.environ['APP_CONFIG_PATH']
full_config_path = '/' + env + '/' + app_config_path
# Initialize app at global scope for reuse across invocations
app = None

class MyApp:
    def __init__(self, config):
        """
        Construct new MyApp with configuration
        :param config: application configuration
        """
        self.config = config

    def get_config(self):
        return self.config

def load_config(ssm_parameter_path):
    """
    Load configparser from config stored in SSM Parameter Store
    :param ssm_parameter_path: Path to app config in SSM Parameter Store
    :return: ConfigParser holding loaded config
    """
    configuration = configparser.ConfigParser()
    try:
        # Get all parameters for this app
        param_details = client.get_parameters_by_path(
            Path=ssm_parameter_path,
            Recursive=False,
            WithDecryption=True
        )

        # Loop through the returned parameters and populate the ConfigParser
        if 'Parameters' in param_details and len(param_details.get('Parameters')) > 0:
            for param in param_details.get('Parameters'):
                param_path_array = param.get('Name').split("/")
                section_position = len(param_path_array) - 1
                section_name = param_path_array[section_position]
                config_values = json.loads(param.get('Value'))
                config_dict = {section_name: config_values}
                print("Found configuration: " + str(config_dict))
                configuration.read_dict(config_dict)

    except:
        print("Encountered an error loading config from SSM.")
```

### Create the stack

Please make sure that you are in region us-east-1 (N. Virginia), otherwise the template will not work.&#x20;

We are going to be creating a stack using a SAM template.

1. Navigate to CloudFormation
2. Click **Create stack** and select **with new resources (standard)**&#x20;
3. Pick **Template is ready**
4. Upload the **ps-template.yml** from your local machine. \
   I recommend opening Designer in a new tab and taking a look at the contents.&#x20;
5. Back in the CloudFormation console, click **Next**
6. Call the stack **PS-lab**
7. Click next a few times till you get to the following "Capabilities and transforms":
8. You will need to check three boxes before you can create the stack.&#x20;

!["I have read and agree to terms and conditions"](<../../.gitbook/assets/image (14).png>)

Wait for a while as the stack gets created.&#x20;

## Lambda console

Once the stack is done, let's head over to the Lambda console to see our function.&#x20;

### Test the Lambda

Create a test event. The default contents are fine, the function is going to ignore it anyway.&#x20;

The result should look like this:

![unencrypted parameter](<../../.gitbook/assets/image (2).png>)

Our function has gone into Parameter store and fetched our unencrypted parameter.&#x20;

## Encrypt your parameter

If we were comfortable with the whole world seeing our parameters, we wouldn't need Parameter Store. What we are going to do is encrypt our parameter. Our Lambda function has the necessary permissions to decrypt it.&#x20;

Head over to Parameter store. Sure enough, there is already a parameter there, created by our stack:

![](<../../.gitbook/assets/image (273).png>)

### Create new parameter

Choose **Create Parameter**.

* For **Name**, enter _/dev/parameterStoreBlog/appSecrets_.
* **Tier** is **Standard**
* For **Type**, select **Secure String**.
* **KMS key source**: My current account&#x20;
* For **KMS Key ID**, choose from the dropdown _alias/ParameterStoreBlogKeyDev_, which is the key that your SAM template created.
* For **Value**, enter `{"secretKey": "secretValue"}`.

The value disappears from view as soon as you move your focus away from the text area. It is replaced by dots.&#x20;

Click **create parameter**.&#x20;

![parameter creation](<../../.gitbook/assets/image (279).png>)

### Try to view Secure parameter

![yikes](<../../.gitbook/assets/image (344).png>)

We have permission to encrypt but not decrypt the parameter value.&#x20;

### Test Lambda again&#x20;

![I see u](<../../.gitbook/assets/image (282).png>)

Note: if you test the Lambda immediately after you create the new parameter, then the Lambda test results might not show the secure parameter. \
You can either wait a little longer or go to Lambda > configuration > edit and set the timeout to a smaller value. Then test again and you should see the secure string being shown, as in the screenshot above.&#x20;

## X-Ray

We haven't discussed X-Ray yet. It is a very useful tool to debug Lambda functions and serverless architectures.&#x20;

In the Lambda console, in the Monitor tab there is a button "View X-Ray logs". Click on it.&#x20;

### Trace map

![](<../../.gitbook/assets/image (251).png>)

![](<../../.gitbook/assets/image (295).png>)

In CloudWatch&#x20;

![](<../../.gitbook/assets/image (151).png>)

## Clean up

Delete the CloudFormation stack.&#x20;

In Parameter store, delete the app secret you created.&#x20;

Delete CloudWatch log group.&#x20;
