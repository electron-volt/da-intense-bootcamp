# LAB: CI/CD pipeline for ECS

## Introduction&#x20;

We are going to create a CI/CD pipeline using AWS developer tools.&#x20;

We will have two separate environments: staging and production.&#x20;

Both environments will have an ECS cluster and an internet-facing ALB.&#x20;

This is a long and complex lab, but for the most part all you need to do is copy text and paste it into a Cloud9 IDE, so it could be the easiest thing you do all year.

![the only keys you need](<../../.gitbook/assets/image (373).png>)

### Before you begin!&#x20;

If you are still in the same region where we built our previous labs:

* Go into the AWS console and delete all except the default VPC. So the only VPC you should have is the one with CIDR 172.31.0.0/16. We will need the CIDR blocks 10.x.x.x for this lab.&#x20;
* Also make sure that your region has support for the EC2 instance type **t3.micro** (it's free-tier in eu-north-1 so I recommend that region).&#x20;

This lab is broken down into smaller sections, which are explained below.&#x20;

## Setting up

We create our Cloud9 IDE and set up our environments using CloudFormation templates.

## CodeCommit&#x20;

We make sure that our Cloud9 IDE has git set up correctly. There is an optional mini-lab about how to use git with CodeCommit.&#x20;

## Build pipeline

We will&#x20;

* create an ECR repo (for container images)
* create a CodeCommit repo (for our source code)&#x20;
* create an ECS task definition and service&#x20;
* create a CodeBuild project
* create the entire pipeline that takes our source from CodeCommit, builds it, stores the image in ECR and deploy it to our ECS service!&#x20;

## Pipeline execution&#x20;

We will execute the pipeline and deploy a change.&#x20;

## Blue/green deployment&#x20;

If we have time, we can change our previous rolling deployment into a CodeDeploy deployment using a blue/green deployment type.

A blue/green deployment is a deployment strategy in which you create two separate (isolated) service environments. One environment (blue) is running the previous application version and one environment (green) is running the new application version.&#x20;

## Clean up
