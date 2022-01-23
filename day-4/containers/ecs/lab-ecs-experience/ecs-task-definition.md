# ECS Task definition

We are going to create three task definitions. Just like we had a Dockerfile that was used to create a Docker image, we will make task definitions that will be used to create tasks.&#x20;

## Sidenote: use OLD GUI &#x20;

You can choose between this

![old ECS experience - use this](<../../../../.gitbook/assets/image (338).png>)

and this:

![New ECS experience - not in this part of the lab ](<../../../../.gitbook/assets/image (115).png>)

For the task definitions, we want to use the **OLD** GUI.&#x20;

That is because the only task definition I ever made using the _new_ GUI went sort of.. completely wrong.&#x20;

## Create task definition "web"

In the ECS console, click Task Definitions > create new Task Definition.&#x20;

### Launch type: EC2

Launch type compatibility: EC2

![EC2 launch type](<../../../../.gitbook/assets/image (93).png>)

### Task settings

* Name: web
* Task role and network mode: leave as default
* Task execution role: None.

![Network mode has to be default, otherwise we will be in trouble. ](<../../../../.gitbook/assets/image (263).png>)

Task size: leave these as blank. They are optional for the EC2 launch type.&#x20;

### Add container

Click Add container.&#x20;

![container settings](<../../../../.gitbook/assets/image (342).png>)

* name: web
* Image URI: public.ecr.aws/ecs-cats-dogs-web/web:latest
* Memory limits: hard limit 128
* Port mappings: Host 0, Container 80

Skip the rest. Click Add.

![web container has been created](<../../../../.gitbook/assets/image (337).png>)

### Scroll to create

Scroll to the bottom of the page and then click Create.&#x20;

## Create task definition "catsdef"

### Get URI of image

For this part you will need the URI of the cats Docker image in your own ECR repo. Go to ECR > repositories > cats, then click Copy URI.

![copy URI of latest cat image](<../../../../.gitbook/assets/image (88).png>)

### Create task definition&#x20;

Proceed to ECS > task definitions > create new task definition.&#x20;

For the **catsdef** task definition, use the same settings as for web task definition. Type is EC2.

Replace the task definition name with **catsdef**.&#x20;

When you get to "add container", name the container **cats** and use as the image URI the URI you just copied from the ECR repo.&#x20;

![settings](<../../../../.gitbook/assets/image (100).png>)

### Add cats container&#x20;

![cats container ](<../../../../.gitbook/assets/image (139).png>)

## Create task definition "dogsdef" - Fargate

For the dogsdef task definition, we want to use Fargate - just to see how that operates.&#x20;

Go to ECS > task definitions > create task definition.&#x20;

### Get image URI&#x20;

You will need the URI of the dogs image from your dogs ECR repo.

### Launch type

We pick Fargate for this

![yeeees serverless](<../../../../.gitbook/assets/image (287).png>)

### Task definition&#x20;

Call it dogsdef and pick Linux as OS.

![](<../../../../.gitbook/assets/image (245).png>)

### Task execution role

Pick ECSTaskExecutionRole

![role](<../../../../.gitbook/assets/image (188).png>)

### Task size

Pick the smallest values

![we're being cheap](<../../../../.gitbook/assets/image (304).png>)

### Add container

Click add container. Call the container dog and use the URI of the latest dogs image from ECR dogs repo.&#x20;

Notice the difference in ports: we cannot specify the host port like we did for EC2 launch type.&#x20;

![container settings](<../../../../.gitbook/assets/image (387).png>)

### Create

Leave all other settings as they are and click create.

## Status

We should have three task definitions: web and catsdef (EC2) and dogsdef (Fargate).&#x20;

![](<../../../../.gitbook/assets/image (315).png>)

