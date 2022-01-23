---
description: Infrastructure as Code
---

# ☁ CloudFormation

* AWS CloudFormation is a service that helps you model and set up your AWS resources.&#x20;
* You create a template that describes all the AWS resources that you want and CloudFormation takes care of provisioning and configuring those resources for you.&#x20;
* You don't need to individually create and configure AWS resources and figure out what's dependent on what; CloudFormation handles that.

**You put the AWS resources you want into a .yml file and AWS does everything else for you.**

So after all the clicking. All the instances we launched. The VPC's, the ASG's, the ALB's.. now you tell me&#x20;

I COULD HAVE DONE IT ALL WITH A STINKING YML FILE?

![Life's hard.](<../../.gitbook/assets/image (341).png>)

## Concepts

### Templates

A CloudFormation template is a JSON or YAML formatted text file. CloudFormation uses these templates as blueprints for building your AWS resources.

### Stacks

When you use CloudFormation, you manage related resources as a single unit called a stack. You create, update, and delete a collection of resources by creating, updating, and deleting stacks. All the resources in a stack are defined by the stack's CloudFormation template.

### Change sets

If you need to make changes to the running resources in a stack, you update the stack. Before making changes to your resources, you can generate a change set, which is a summary of your proposed changes. Change sets allow you to see how your changes might impact your running resources, especially for critical resources, before implementing them.
