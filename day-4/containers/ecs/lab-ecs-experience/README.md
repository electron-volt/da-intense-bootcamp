# LAB: ECS experience

How do you feel about building something like this:

![](<../../../../.gitbook/assets/image (384).png>)

Note: this lab is not entirely in the free tier. There are resources like the NAT Gateway that do cost money. Also some instances used are not free-tier eligible.

I built this environment from start to finish in the Ireland region and it cost me less than $1.50, so I wouldn't call it expensive.&#x20;

## Agenda

In this lab we will&#x20;

* Manage Docker images of **cats** and **dogs** in Amazon ECR.
* Create Amazon ECS **cluster, task definitions**, and **services**.
* Choose right **launch type** - either **EC2** or **Fargate** - for each service.
* Deploy simple containerized application **cats and dogs,** path-routed through an ALB.
* Monitor ECS cluster and services in Amazon CloudWatch **Container Insights**.
* Centralize **container logs** using **AWS FireLens** and Amazon CloudWatch Logs.
* Scale ECS **services** and **cluster** automatically.

## Sections

We will break this down into smaller sections.

### CloudFormation and Cloud9

We create a CloudFormation stack that provisions our VPC and an EC2 instance. This EC2 instance is for our Cloud9 IDE environment.&#x20;

The CloudFormation stack also clones a GitHub repository into our Cloud9 IDE.

We then give the EC2 instance the necessary IAM permissions and configure our IDE.

### ECR

We will create two ECR repositories called **cats** and **dogs**. Then we will go into the Cloud9 IDE and locate two Dockerfiles that got cloned from the GitHub repo.&#x20;

We will build the Dockerfiles into Docker images and push these images into our ECR repos.&#x20;

### ECS Cluster

We set up an ECS cluster.&#x20;

### ECS Task definition

We create three task definitions: web, dogsdef that use EC2 launch type and catsdef that uses Fargate launch type.&#x20;

### ECS Service

We will create an Application Load Balancer and three ECS services. We will use our familiar path-based routing to route traffic to different services.&#x20;

