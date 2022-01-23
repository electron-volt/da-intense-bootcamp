# Setting up

## Create Cloud9 IDE

Create an environment called **cicd-lab**. (Or call it whatever).

Proceed with defaults: direct access, free-tier eligible instance.&#x20;

If you want to customize your IDE, you can go to Preferences > user settings > terminal and increase the font size, change colours etc. &#x20;

### Install jq

First install jq to help with JSON:

```
sudo yum -y install jq gettext
```

### Useful script

This script defines a function that records a trail of all environment variables that are needed in the course of the workshop. Just copy the whole thing and paste into the terminal:

```clike
cat <<EoF > ~/env-vars.sh
#!/bin/bash

save_var() {
  if [ \$? -eq 0 ]; then
    export \$1=\$2
    echo export \$1=\$2 >> ~/env-vars.sh
  fi
}
EoF
chmod +x ~/env-vars.sh
source ~/env-vars.sh
echo "[[ -s ~/env-vars.sh ]] && source ~/env-vars.sh" >> ~/.bash_profile

```

### Region and account ID

Configure AWS CLI with our current region as default region:

```
save_var AWS_REGION $(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r .region) 
aws configure set default.region ${AWS_REGION} 
aws configure get default.region
```

Set up environment variable for your account ID

```c
save_var AWS_ACCOUNT_ID $(aws sts get-caller-identity --query Account --output text)
```

### Download resources

We will clone a git repo with all the necessary resources.&#x20;

```
cd ~/environment 
git clone https://github.com/electron-volt/cicd-for-ecs-workshop-code.git
```

### Enable CloudWatch container insights

```
aws ecs put-account-setting-default --name containerInsights --value enabled
```

## Environments&#x20;

### IAM roles

The following commands will check if we have the IAM roles that we are going to need for our load balancers and ECS. The double pipe || means that if the roles do not exist, they get created.&#x20;

```
aws iam get-role --role-name "AWSServiceRoleForElasticLoadBalancing" || aws iam create-service-linked-role --aws-service-name "elasticloadbalancing.amazonaws.com"
aws iam get-role --role-name "AWSServiceRoleForECS" || aws iam create-service-linked-role --aws-service-name "ecs.amazonaws.com"
```

### CloudFormation templates

Once again we let CloudFormation do all the hard work while we go and sip some coffee. First change directories with this command (this is the git repo we cloned earlier):

```
cd ~/environment/cicd-for-ecs-workshop-code/setup 
```

You can view the contents of the directory and you will see some .yaml files. Then run these commands (they run in the background thanks to the & so you get your prompt back).&#x20;

```
aws cloudformation deploy \
  --stack-name prod-cluster \
  --template-file Cluster-ECS-EC2-2AZ-ALB-1NAT.yaml \
  --parameter-overrides "EnvironmentName=prod" \
  --capabilities CAPABILITY_IAM &
aws cloudformation deploy \
  --stack-name staging-cluster \
  --template-file Cluster-ECS-EC2-2AZ-ALB-1NAT.yaml \
  --parameter-overrides "EnvironmentName=staging" \
  --capabilities CAPABILITY_IAM &  
```

Over at the CloudFormation console you can see the two stacks get created:

![CF stacks](<../../.gitbook/assets/image (333).png>)

While these stacks are creating, we can move on to CodeCommit.&#x20;
