# Clean up

## CloudFormation&#x20;

Delete the stacks.

This will delete the VPC along with the NAT gateway.



## ALB

Delete the ALB

then delete the target groups

## ECS

Stop all tasks

Delete services

Delete cluster

Task definitions: these cannot be deleted at the moment. They can only be deregisted.&#x20;

![Deregister task definition. ](<../../../../.gitbook/assets/image (147).png>)

## ECR

Delete repos

