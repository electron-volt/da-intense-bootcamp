# incomplete: CDK and ECS Fargate

Technologies used: Amazon CloudWatch, AWS CodeCommit, AWS CodeBuild, AWS CodePipeline, Amazon ECR, AWS Lambda, Elastic Container Registry (ECR), Elastic Container Service (ECS).

![Architecture](<../.gitbook/assets/image (224).png>)

## Cloud9 IDE

FargateIDE

m4.large



IAM&#x20;

create role

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "FullAccess",
            "Effect": "Allow",
            "Action": [
                "s3:*", 
                "kafka:*", 
                "rds:*", 
                "ecr:*", 
                "ecs:*", 
                "ec2:*", 
                "servicediscovery:*", 
                "lambda:*", 
                "ssm:*", 
                "codepipeline:*", 
                "codecommit:*", 
                "codebuild:*", 
                "cloudwatch:*", 
                "transfer:*", 
                "elasticloadbalancing:*",
                "cloudformation:*",
                "autoscaling:*",
                "application-autoscaling:*",
                "iam:*",
                "events:*",
                "logs:*",
                "route53:*",
                "codedeploy:*",
                "kms:*"
            ],
            "Resource": ["*"]
        }
    ]
}
```

create policy, create role

assign role to cloud9 instance.&#x20;

## CDK

disable AWS temp credential&#x20;

```
rm -vf ${HOME}/.aws/credentials
```

git clone

```
git clone https://github.com/aws-samples/amazon-ecs-fargate-workshop-dev-ops-data
```

commit parts&#x20;

```
mkdir ~/environment/fargate-dev-workshop
cp -r ~/environment/amazon-ecs-fargate-workshop-dev-ops-data/labs/fargate-dev-workshop/* ~/environment/fargate-dev-workshop/
cd ~/environment/fargate-dev-workshop
```

while in the f-d-w dir

```
npm install -g aws-cdk@latest
```

update pip

```
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python get-pip.py
```

Install CDK modules

```
pip install awscli
pip install --upgrade aws-cdk.core
pip install -r requirements.txt 
```

Create a config file and put your region and account ID there:

```
touch ~/.aws/config
nano ~/.aws/config

[default]
region=us-west-2
account=account-id
```

issues with cdk version prevent me from continuing&#x20;
