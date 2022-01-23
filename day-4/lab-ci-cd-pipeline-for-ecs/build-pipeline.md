# Build pipeline

![](<../../.gitbook/assets/image (55).png>)

We are going to create a CodePipeline with three stages: source, build and deploy. You can see in this _expertly drawn_ diagram how we will utilize different AWS technologies.&#x20;

We have seen how most of these technologies work in the console, so now let's use Cloud9 and the CLI instead.&#x20;

## ECR repository

In the Cloud9 IDE, run this to create an ECR repo called **hello-server:**

```
save_var IMAGE_REPO_URI $( \
    aws ecr create-repository \
        --repository-name hello-server \
        --query repository.repositoryUri \
        --output text \
)
echo Repo URI: $IMAGE_REPO_URI

```

It should return the name of an ECR repo of the form&#x20;

account-id.dkr.ecr.eu-north-1.amazonaws.com/hello-server.

You can also verify that your repo has been created with&#x20;

```
aws ecr describe-repositories --repository-name hello-server
```

## CodeCommit repo&#x20;

Now let's create a CodeCommit repo called **hello-server**:

```
save_var SRC_REPO_URL $( \
    aws codecommit create-repository \
        --repository-name hello-server \
        --query repositoryMetadata.cloneUrlHttp \
        --output text \
)
echo New repo created at $SRC_REPO_URL
```

You can verify your repo has been created by running

```
aws codecommit get-repository --repository-name hello-server
```

## Clone CodeCommit repo

Let's create a directory called **hello-server** and clone our empty CodeCommit repo there:

```
cd ~/environment 
mkdir hello-server 
git clone $SRC_REPO_URL hello-server
```

Next we want to copy some necessary files from the first git repo we cloned into our new CodeCommit repo:

```
cp ~/environment/cicd-for-ecs-workshop-code/app/hello-server/* .
```

If you view the contents of the directory now with ls, you will see our Dockerfile. The first line is `FROM: node:buster-slim`. This is getting a base image from Dockerhub. We can get it from ECR instead, so run this:

```
sed -i 's%node:buster-slim%public.ecr.aws/wizriz/node:buster-slim%' Dockerfile '
cat Dockerfile
```

The first line is now `FROM public.ecr.aws/wizriz/node:buster-slim.`

### Buildspec

As we know, we will need a Buildspec file if we want to use CodeBuild. Let's copy an existing Buildspec from the first git repo into our current directory:

```
cp  ~/environment/cicd-for-ecs-workshop-code/lab-1/buildspec-hello-server.yml ./buildspec.yml
```

Take a moment to read through the buildspec. Notice in the pre-build phase there is no REPOSITORY\_URI? We can fix that:

```
sed -i "s%<IMAGE_REPO_URI>%$IMAGE_REPO_URI%" buildspec.yml
cat buildspec.yml
```

Now it should say REPOSITORY\_URI=account-if.dkr.ecr.eu-north-1.amazonaws.com/hello-server.

Then push changes to the CodeCommit repo:

```
git add .
git commit -m "First commit"
git push -u origin master
```

Our repositories are now good to go.&#x20;

## ECS resources

### CloudWatch log group

We will need a log group that our containers will send their logs to. Create one like so:

```
aws logs create-log-group --log-group-name /ecs/hello-server
```

### Task definition

We have gone through the back-breaking labour of creating a task definition via the console. Now let's instead use a JSON file. All you need to do is run this:

```
cd ~/environment 
mkdir lab-1 
cd lab-1 
envsubst < ~/environment/cicd-for-ecs-workshop-code/lab-1/task-definition-hello-server.json.template > task-definition.json 
cat task-definition.json
```

Take a moment to read the JSON that pops out. EC2, port 80, uses our ECR image.. that is how easy it can be.&#x20;

We will still need to register our task definition. Run this;

```
 aws ecs register-task-definition --cli-input-json file://task-definition.json
```

The revision number should be 1.&#x20;

![revision is 1.](<../../.gitbook/assets/image (72).png>)

### ECS service

Make sure that both CloudFormation stacks have completed successfully. There should be an application load balancer with a dummy target group in both the staging and prod environments.&#x20;

#### Target group

Next we will create an actual target group for our ECS service. Just copy this, paste it into Cloud9 and hope for the best:

```
save_var STAGING_VPC $( \
  aws cloudformation describe-stacks \
  --stack-name staging-cluster \
  --query "Stacks[0].Outputs[?OutputKey == 'VpcId'].OutputValue" \
  --output text \
)
save_var STAGING_TARGET_GROUP_ARN $( \
  aws elbv2 create-target-group \
  --name hello-server-tg \
  --port 80 \
  --protocol HTTP \
  --health-check-path /ping \
  --health-check-timeout-seconds 3 \
  --health-check-interval-seconds 5 \
  --healthy-threshold-count 2 \
  --target-type instance \
  --vpc-id $STAGING_VPC \
  --query "TargetGroups[0].TargetGroupArn" \
  --output text \
)
aws elbv2 modify-target-group-attributes \
  --target-group-arn $STAGING_TARGET_GROUP_ARN \
  --attributes "Key=deregistration_delay.timeout_seconds,Value=5"

```

I wish everything in my life were this easy.&#x20;

#### Listener&#x20;

Next we make a listener:

```
cat <<EoF >conditions-pattern.json
[
    {
        "Field": "path-pattern",
        "PathPatternConfig": {
            "Values": ["/hello/*"]
        }
    }
]
EoF
cat <<EoF >actions.json
[
    {
        "Type": "forward",
        "ForwardConfig": {
            "TargetGroups": [ { "TargetGroupArn": "${STAGING_TARGET_GROUP_ARN}" } ]
        }
    }
]
EoF
save_var STAGING_LISTENER_ARN $( \
  aws cloudformation describe-stacks \
  --stack-name staging-cluster \
  --query "Stacks[0].Outputs[?OutputKey == 'PublicListener'].OutputValue" \
  --output text \
)
save_var STAGING_LISTENER_RULE_ARN $( \
    aws elbv2 create-rule \
    --listener-arn $STAGING_LISTENER_ARN \
    --priority 10 \
    --conditions file://conditions-pattern.json \
    --actions file://actions.json \
    --query "Rules[0].RuleArn" \
    --output text \
)
```

#### Staging ECS service&#x20;

Now the actual ECS service, which we define with a JSON file:

```
envsubst \
  < ~/environment/cicd-for-ecs-workshop-code/lab-1/service-hello-server.json.template \
  > staging_service.json
cat staging_service.json
```

and then create:

```
aws ecs create-service --cli-input-json file://staging_service.json
```

#### Take a look at the console

In the ECS console you see two ECS clusters created by Cloudformation:

![clusters](<../../.gitbook/assets/image (350).png>)

The staging cluster has a service, the one we just created, but no tasks. That is because the ECR image that is specified in the task definition is empty.&#x20;

Our pipeline is going to build the image and put in into ECR. Then the task definition will begin to use it automatically.&#x20;

## CodeBuild project prerequisites

CodeBuild will need a log group in CloudWatch that it can send logs to. It will also need an IAM role with the necessary permissions. Time to copy-paste these into our terminal:

```
aws logs create-log-group --log-group-name /codebuild/hello-server
cat <<EoF > codebuild_trust_policy_doc.json
{
  "Version": "2008-10-17",
  "Statement": [
    {
      "Sid": "",
      "Effect": "Allow",
      "Principal": {
        "Service": "codebuild.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
EoF
save_var CODEBUILD_ROLE_NAME Cloud9-CodeBuild-Role-$(date +%s)
save_var CODEBUILD_ROLE_ARN $( \
    aws iam create-role \
        --role-name $CODEBUILD_ROLE_NAME \
        --assume-role-policy-document file://codebuild_trust_policy_doc.json \
        --query Role.Arn \
        --output text \
)
aws iam attach-role-policy \
   --role-name $CODEBUILD_ROLE_NAME \
   --policy-arn arn\:aws\:iam::aws\:policy/AmazonEC2ContainerRegistryPowerUser
```

First on line 1 we create the log group.&#x20;

Lines 2-16: we create the policy JSON that the role will use.

Line 17: make sure the role has a unique name by appending date to it

Lines 18-24: create the IAM role and save its ARN in a variable

Lines 25-27: attach the policy we made to the role we created.&#x20;

We will create the actual build project in the next phase. For now, let's move into the console and create our pipeline.&#x20;

## Create pipeline

We will create our pipeline in the console. You can leave the Cloud9 IDE running and go back into the console, then navigate to CodePipeline.

### Create pipeline

Create pipeline.

Name it **hello-server-pipeline.**

Check "Allow Pipeline to create a service role..." and accept the default name for the role.&#x20;

### Source stage

![source stage](<../../.gitbook/assets/image (83).png>)

As source we use the master branch of our CodeCommit repo.&#x20;

### Build stage

You arrive at the build stage. Here is what it looks like:

![build stage](<../../.gitbook/assets/image (98).png>)

Pick CodeBuild as build provider. Then click the **Create project** button to open a new dialogue in a new tab.&#x20;

Name: **hello-server-build**

Under Environment, use these settings:

![environment settings](<../../.gitbook/assets/image (104).png>)

Managed image, Amazon Linux, standard runtime, pick version 2.0 (I don't know 100% that the lab works with 3.0), "check your privilege", pick existing role and find the one that starts with cloud9.&#x20;

#### Buildspec

Make sure "use a buildspec file" is selected. Leave the name blank.&#x20;

#### Logs

Check the CloudWatch logs. Use codebuild/hello-server as group name.

Then click "return to CodePipeline". This is what you'll see:

![build project has been created](<../../.gitbook/assets/image (343).png>)

Then click next.&#x20;

### Deploy stage

For the deploy stage, pick ECS as the provider.&#x20;

Ensure that region is correct.&#x20;

Cluster: **staging**

service: **hello-server.**&#x20;

Leave other fields as blank.&#x20;

![deploy stage](<../../.gitbook/assets/image (374).png>)

Click next - review - create. Pipeline has been created!
