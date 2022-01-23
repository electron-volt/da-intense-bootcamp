# ECS

## ECS concepts

![Well that clears it up. ](<../../../.gitbook/assets/image (91).png>)

### Task definition

To prepare your application to run on Amazon ECS, you create a _task definition_. The task definition is a text file, in JSON format, that describes 1-10 containers that form your application.

You can also create task definitions manually via the GUI (we will do this later).&#x20;

Task definitions specify various parameters for your application, including for example:

* which containers to use
* which launch type to use (Fargate or EC2)
* what CPU and memory resources are required
* which ports should be opened for your application
* what data volumes should be used with the containers in the task.

Example task definition that contains a single container that runs a web server using the Fargate launch type.&#x20;

```json
{
    "family": "webserver",
    "containerDefinitions": [
        {
            "name": "web",
            "image": "nginx",
            "memory": "100",
            "cpu": "99"
        },
    ],
    "requiresCompatibilities": [
        "FARGATE"  # Fargate launch type - serverless
    ],
    "networkMode": "awsvpc",
    "memory": "512",  
    "cpu": "256",
}
```

### Task

A _task_ is the instantiation of a task definition within a cluster. We've seen how to launch an EC2 instance from an AMI: the AMI is like the task definition and the instance that is launched is like a task.&#x20;

### Cluster

An Amazon ECS cluster is a logical grouping of tasks or services.

### Service

An Amazon ECS service allows you to run and maintain a specified number of instances of a task definition simultaneously in an Amazon ECS cluster. If any of your tasks should fail or stop for any reason, the Amazon ECS service scheduler launches another instance of your task definition to replace it in order to maintain the desired number of tasks in the service.

Sort of like an auto-scaling group that launches EC2 instances based on a launch template.&#x20;

### Launch type

There are two options to choose from:

#### Fargate&#x20;

Fargate is a serverless way to run containers. You do not have access to the underlying hardware. AWS handles provisioning, configuring and scaling on your behalf.

#### EC2&#x20;

With this launch type the containers that you run are running on EC2 instances. You manage these instances yourself and then register them with your ECS cluster.&#x20;
