---
description: First taste of CodeDeploy
---

# Blue/green deployment

![new architecture](<../../.gitbook/assets/image (22).png>)

We will add two new stages to our pipeline:

* Approve
* second deploy.&#x20;

The first deployment is into the staging environment, as before. Then there is an approval stage and once approved, the changes are deployed via CodeDeploy into the production environment.&#x20;

## Blue/green deployment

### Target groups

We are going to go back to our Cloud9 IDE. Let's paste in the following:

```
save_var PROD_VPC $( \
  aws cloudformation describe-stacks \
  --stack-name prod-cluster \
  --query "Stacks[0].Outputs[?OutputKey == 'VpcId'].OutputValue" --output text \
)
save_var PROD_BLUE_TG_ARN $( \
  aws elbv2 create-target-group \
  --name hello-server-blue \
  --port 80 \
  --protocol HTTP \
  --health-check-path /ping \
  --health-check-timeout-seconds 3 \
  --health-check-interval-seconds 5 \
  --healthy-threshold-count 2 \
  --target-type instance \
  --vpc-id $PROD_VPC \
  --query "TargetGroups[0].TargetGroupArn" \
  --output text \
)
aws elbv2 modify-target-group-attributes \
  --target-group-arn $PROD_BLUE_TG_ARN \
  --attributes "Key=deregistration_delay.timeout_seconds,Value=5"
save_var PROD_GREEN_TG_ARN $( \
  aws elbv2 create-target-group \
  --name hello-server-green \
  --port 80 \
  --protocol HTTP \
  --health-check-path /ping \
  --health-check-timeout-seconds 3 \
  --health-check-interval-seconds 5 \
  --healthy-threshold-count 2 \
  --target-type instance \
  --vpc-id $PROD_VPC \
  --query "TargetGroups[0].TargetGroupArn" \
  --output text \
)
aws elbv2 modify-target-group-attributes \
  --target-group-arn $PROD_GREEN_TG_ARN \
  --attributes "Key=deregistration_delay.timeout_seconds,Value=5"
```

What it does it creates two target groups in the production VPC created by the CloudFormation stack prod.&#x20;

Here are all our target groups now:

![target groups](<../../.gitbook/assets/image (246).png>)

### Listener

Create the listener that forwards traffic to the blue target group:

```
cd ~/environment
mkdir lab-2
cd lab-2
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
cat <<EoF >actions_blue.json
[
    {
        "Type": "forward",
        "ForwardConfig": {
            "TargetGroups": [ { "TargetGroupArn": "$PROD_BLUE_TG_ARN" } ]
        }
    }
]
EoF
save_var PROD_LISTENER_ARN $( \
  aws cloudformation describe-stacks \
  --stack-name prod-cluster \
  --query "Stacks[0].Outputs[?OutputKey == 'PublicListener'].OutputValue" \
  --output text \
)
save_var PROD_LISTENER_RULE_ARN $( \
  aws elbv2 create-rule \
  --listener-arn $PROD_LISTENER_ARN \
  --priority 10 \
  --conditions file://conditions-pattern.json \
  --actions file://actions_blue.json  \
  --query "Rules[0].RuleArn" \
  --output text \
)

```

In the console we can see that the prod ALB has this new rule:

![prod ALB listener rules. ](<../../.gitbook/assets/image (177).png>)

### CloudWatch log group

When creating our task definition we will need to specify a CloudWatch Log Group to receive logs from our containers.

Create a log group `/ecs/hello-server-prod` using:

```
aws logs create-log-group --log-group-name /ecs/hello-server-prod
```

### Task definition for production service

Create a task definition spec using the supplied JSON template, and register the task definition.

```
cd ~/environment/lab-2
envsubst < ~/environment/cicd-for-ecs-workshop-code/lab-2/taskdef-prod-hello-server.json.template > taskdef-prod.json
cat taskdef-prod.json
aws ecs register-task-definition --cli-input-json file://taskdef-prod.json
```

There it is:

![](<../../.gitbook/assets/image (222).png>)

### ECS service for production&#x20;

We have the task def, now we need the production service. Here is the json that specifies the service:

```
cd ~/environment/lab-2
envsubst < ~/environment/cicd-for-ecs-workshop-code/lab-2/service-prod-hello-server.json.template > service-prod.json
cat service-prod.json
```

Then we create the service

```
aws ecs create-service --service-name hello-server-prod --cli-input-json file://service-prod.json
```

Service is up, but no tasks:

![prod service](<../../.gitbook/assets/image (237).png>)

Let's test this new service.

Option 1: in the IDE terminal, run the following - substitute your own name on line 7

```
save_var PROD_ALB_URL $( \
  aws cloudformation describe-stacks \
  --stack-name prod-cluster \
  --query "Stacks[0].Outputs[?OutputKey == 'ExternalUrl'].OutputValue" \
  --output text \
)
curl $PROD_ALB_URL/hello/namehere
```

Option 2: get the URL of your production ALB with this

```
echo $PROD_ALB_URL
```

copy the URL, add /hello/yourname to it like so

{% embed url="http://prod-alb-119909790.eu-north-1.elb.amazonaws.com/hello/namehere" %}
example url
{% endembed %}

and open in a browser.&#x20;

### CodeDeploy

We will add two configuration files `taskdef-prod.json` and `appspec.yaml` to our application source repo. Later, we will see how they are picked up by the CodePipeline _CodeDeployToECS_ action, modified to fill in placeholder values, and passed on to CodeDeploy.

Switch to your local app repo and copy in the required files:

```
cd ~/environment/hello-server
cp ../lab-2/taskdef-prod.json .
cp ~/environment/cicd-for-ecs-workshop-code/lab-2/appspec-hello-server.yaml ./appspec.yaml
```

We need to make a change to taskdef-prod.json. We need to replace the current value of the "image" attribute with a placeholder value \<IMAGE\_NAME>. CodeDeploy will then look for this value and replace it with the correct image location and tag at deployment time.

You can make this change using `jq` as follows:

```
mv taskdef-prod.json old-taskdef-prod.json
jq '.containerDefinitions[0].image="<IMAGE_NAME>"' old-taskdef-prod.json > taskdef-prod.json
rm old-taskdef-prod.json
cat taskdef-prod.json
```

The line has been changed now:

![placeholder is in place](<../../.gitbook/assets/image (34).png>)

Notice also the contents of the appspec.yaml file:

![appspec.yaml in all its glory](<../../.gitbook/assets/image (255).png>)

#### Add files to git commit

`git add -A`

do not commit or push yet tho.&#x20;

### Create CodeDeploy application&#x20;

#### Create service role

```
cd ~/environment/lab-2
cat <<EoF > codedeploy_trust_policy_doc.json
{
  "Version": "2008-10-17",
  "Statement": [
    {
      "Sid": "",
      "Effect": "Allow",
      "Principal": {
        "Service": "codedeploy.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
EoF
```

Now create the role and attach the `AWSCodeDeployRoleForECS` managed policy:

```
save_var CODEDEPLOY_ROLE_NAME Cloud9-CodeDeploy-Role-$(date +%s)
save_var CODEDEPLOY_ROLE_ARN $( \
    aws iam create-role \
        --role-name $CODEDEPLOY_ROLE_NAME \
        --assume-role-policy-document file://codedeploy_trust_policy_doc.json \
        --query Role.Arn \
        --output text \
)
aws iam attach-role-policy \
   --role-name $CODEDEPLOY_ROLE_NAME \
   --policy-arn arn\:aws\:iam::aws\:policy/AWSCodeDeployRoleForECS
```

#### Create CodeDeploy application in the console

Let's go back to the AWS console now and navigate to CodeDeploy.

Click Create application.&#x20;

Pick a name, like hello-server

Compute platform: Amazon ECS

Create application.&#x20;

![easy](<../../.gitbook/assets/image (66).png>)

You will be taken to your new CodeDeploy application. You should have tab Deployment groups open. Click Create deployment group.&#x20;

![settings](<../../.gitbook/assets/image (9).png>)

Group name: hello-server-prod

Service role: starts with cloud9

ECS cluster: prod, service hello-server-prod.

![settings](<../../.gitbook/assets/image (3).png>)

For ALB, choose **prod-ALB**.

Listener port is HTTP 80.

Target group 1: **hello-server-blue**. TG2: **hello-server-green.**&#x20;

Deployment: AllAtOnce

Original revision termination is 1 hour, keep it.&#x20;

Create deployment group.&#x20;

### Modify buildspec.yml

In the Cloud9 text editor, open hello-server/buildspec.yml.

**Note: DO NOT change line 13, which has your REPOSITORY\_URI.**

Replace the other contents with the following. When editing YAML manually, please make sure the indentation is correct - whitespace matters.&#x20;

{% code title="buildspec.yml" %}
```yaml
version: 0.2

env:
  exported-variables:
    - AWS_DEFAULT_REGION

phases:
  pre_build:
    commands:
      - echo Logging in to Amazon ECR...
      - aws --version
      - $(aws ecr get-login --region $AWS_DEFAULT_REGION --no-include-email)
      - REPOSITORY_URI= DO NOT CHANGE ME BRO.dkr.ecr.eu-north-1.amazonaws.com/hello-server
      - COMMIT_HASH=$(echo $CODEBUILD_RESOLVED_SOURCE_VERSION | cut -c 1-7)
      - IMAGE_TAG=${COMMIT_HASH:=latest}
  build:
    commands:
      - echo Build started on `date`
      - echo Building the Docker image...
      - docker build -t $REPOSITORY_URI:latest .
      - docker tag $REPOSITORY_URI:latest $REPOSITORY_URI:$IMAGE_TAG
  post_build:
    commands:
      - echo Build completed on `date`
      - echo Pushing the Docker images...
      - docker push $REPOSITORY_URI:latest
      - docker push $REPOSITORY_URI:$IMAGE_TAG
      - echo Writing image definitions file...
      - printf '[{"name":"hello-server","imageUri":"%s"}]' $REPOSITORY_URI:$IMAGE_TAG > imagedefinitions.json
      - printf '{"ImageURI":"%s"}' $REPOSITORY_URI:$IMAGE_TAG > imageDetail.json
artifacts:
    files: 
      - imagedefinitions.json
      - imageDetail.json
      - appspec.yaml
      - taskdef-prod.json
```
{% endcode %}

When you are done, save and close the file. Then add changes to commit:

```
cd ~/environment/hello-server
git add .
```

Do not commit or push changes yet (it would trigger the pipeline which is not complete).&#x20;

### Add new Approve stage

Go to CodePipeline. Find your pipeline and click on it. Then click the Edit button.&#x20;

Add a stage after Deploy:

![](<../../.gitbook/assets/image (62).png>)

Call the stage Approve.

Then in the new Approve stage, add action group.&#x20;

We want to end up here:

![](<../../.gitbook/assets/image (353).png>)

URL and comments are as follows:

```
https://console.aws.amazon.com/codesuite/codecommit/repositories/hello-server/commit/#{SourceVariables.CommitId}?region=#{BuildVariables.AWS_DEFAULT_REGION}
```

```
Commit message: #{SourceVariables.CommitMessage}
```

Then click done and save changes.&#x20;

### Add a DeployProd stage

To complete our pipeline we add a second Deploy stage, which will implement Blue/Green deployment to the production cluster.

* At the top of the pipeline page, choose **Edit**.
* Scroll to the bottom of the pipeline page, and underneath the **Approve** stage select **Add stage**.
* For **Stage name** enter **DeployProd**.
* Choose **Add Stage**

Create and configure the stage action as follows:

* Choose the **Add action group** button in the **DeployProd** stage.
* For **Action Name** use **DeployProd**.
* For **Action provider** choose **Amazon ECS (Blue/Green)** (a.k.a. the _CodeDeployToECS_ action).
* For **Input artifacts** choose **BuildArtifact**.
* For **AWS CodeDeploy application name** choose **hello-server**.
* For **AWS CodeDeploy deployment group** choose **hello-server-prod**.
* For **Amazon ECS task definition**:
  * Select **BuildArtifact**
  * Enter the filename **taskdef-prod.json**
* For **AWS CodeDeploy AppSpec file**:
  * Select **BuildArtifact**
  * Enter the filename **appspec.yaml**
* For **Input artifact with image details**:
  * Select **BuildArtifact**.
  * For **Placeholder text in the task definition** enter **IMAGE\_NAME**. Recall that this corresponds with the image placeholder name we used in the `taskdef-prod.json` file in step 5.
* Choose **Done** in the **DeployProd** action.
* Choose **Done** in the **DeployProd** stage.
* At the top of the page, choose **Save**.
* Confirm **Save**.

## Push changes

Let's go to Cloud9. Make sure you are in the hello-server directory. Now we can commit with&#x20;

git commit -m "configuration changes"

git push origin master

And go check out what is happening in the CodePipeline!&#x20;

Wait for the pipeline to get to the Approve stage.&#x20;
