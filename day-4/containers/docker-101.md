# Docker 101

![I hope this isn't copyrighted](<../../.gitbook/assets/image (60).png>)

## Image

An _image_ is a read-only template with instructions for creating a Docker container. Often, an image is _based on_ another image, with some additional customization. For example, you may build an image which is based on the `ubuntu` image, but installs the Apache web server and your application, as well as the configuration details needed to make your application run.

## Dockerfile

To build your own image, you create a _Dockerfile_ with a simple syntax for defining the steps needed to create the image and run it. Each instruction in a Dockerfile creates a layer in the image.&#x20;

## **Containers**

A container is a runnable instance of an image. You can create, start, stop, move, or delete a container using the Docker API or CLI. You can connect a container to one or more networks, attach storage to it, or even create a new image based on its current state.

A container is defined by its image as well as any configuration options you provide to it when you create or start it.&#x20;

## Registry&#x20;

An image registry is a centralized place where you can upload your images and can also download images created by others. Docker Hub is the default public registry for Docker. **** AWS has a registry called Elastic Container Registry (ECR).

Learn more about Docker and containers here [https://www.freecodecamp.org/news/the-docker-handbook/](https://www.freecodecamp.org/news/the-docker-handbook/)&#x20;

## Why use containers?&#x20;

Containers are a convenient way to package and deploy applications. Benefits include things like consistency (as opposed to "works on my machine"), speed and portability.&#x20;
