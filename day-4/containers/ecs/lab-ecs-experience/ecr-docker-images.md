# ECR, Docker images

## Create repositories

Navigate to Amazon ECR.&#x20;

### First repo

Click **create repository**

Create a _private_ repository called **dogs**.

Leave all the toggles as disabled and click **create repository**.

### Second repo

Repeat the exact same steps with same settings, except this time the name of the repo is **cats**.

![private animal repos](<../../../../.gitbook/assets/image (15).png>)

## Build Docker images

Go back to the Cloud9 IDE. We have already cloned a Github repo. It contains all kinds of resources, including Dockerfiles that can be used to build Docker images.&#x20;

### Find Dockerfile

In the terminal, run these commands to see the contents of the cats Dockerfile:

```
cd ecsworkshop/cats
cat Dockerfile
```

Here is a longer walkthrough:

```
~/environment $ ls
ecsworkshop  README.md
~/environment $ cd ecsworkshop/  
~/environment/ecsworkshop (master) $ ls
cats  cluster_loadtest.sh  dogs  ilovecats.jpg  ilovedogs.jpg  README.md  service_loadtest.sh  web
E~/environment/ecsworkshop (master) $ cd cats
~/environment/ecsworkshop/cats (master) $ ls
default.conf  Dockerfile  index.html
~/environment/ecsworkshop/cats (master) $ cat Dockerfile
FROM nginx
EXPOSE 80
RUN apt-get update -y && \
  apt-get upgrade -y && \
  apt-get install -y curl && \
  cd /tmp && \
  apt-get install awscli -y && \
  rm -rf /tmp/* && \
  rm -rf /var/lib/apt/lists/*
COPY ./default.conf /etc/nginx/conf.d/default.conf
COPY ./index.html /usr/share/nginx/html/index.html
CMD nginx -g "daemon off;"Eve:~/environment/ecsworkshop/cats (master) $ 
```

If you are new to Dockerfiles, here you can find more information [https://docs.docker.com/engine/reference/builder/#dockerfile-reference](https://docs.docker.com/engine/reference/builder/#dockerfile-reference)

### Build cats image

Here is how we turn the Dockerfile into a Docker image:

```
docker build -t cats . 
```

### Build dogs image

You can build the dogs Docker image using the exact same steps as above, just replace cats with dogs.&#x20;

```
cd ../dogs && docker build -t dogs .
```

## Push images to ECR

So we have our ECR repo where we can store Docker images. We have the images. Now what we need to do is to push our images into our repo.&#x20;

### Push to Dogs repo

Navigate to ECR. We will do this first for the dogs repo, but the steps are exactly the same for cats.&#x20;

Click on the **dogs** repo.&#x20;

On the upper right hand corner, click on the **view push commands** button.&#x20;

You will see this:

![](<../../../../.gitbook/assets/image (201).png>)

Very simple: just run these commands (except the 2. as we have already built the dockerfiles) in the cloud9 IDE.&#x20;

#### Retrieve an authentication token...&#x20;

Note: if you are planning on getting certified, this bit will probably be in the exam. It's a trick question that often shows up.&#x20;

They will ask you how to authenticate to ECR. They provide the command aws ecr get-login-password and the answer options will be

* run the command (wrong answer)
* run the OUTPUT of the command  <- this is the correct answer&#x20;
* \+ 2 other wrong answers.

The `get-login-password` returns something that you then have to pipe to the command `docker login`. So you first run `get-login-password` and then use its OUTPUT to login.

Sneaky.

#### Build your Docker.. SKIP THIS ONE AS WE ALREADY DID IT

Here is what running these commands looks like:

```
~/environment/ecsworkshop (master) $ aws ecr get-login-password --region eu-west-1 | docker login --username AWS --password-stdin account-id.dkr.ecr.eu-west-1.amazonaws.com
WARNING! Your password will be stored unencrypted in /home/ec2-user/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded

~/environment/ecsworkshop (master) $ docker tag dogs:latest accnt-id.dkr.ecr.eu-west-1.amazonaws.com/dogs:latest
~/environment/ecsworkshop (master) $ docker push accnt-id.dkr.ecr.eu-west-1.amazonaws.com/dogs:latest
The push refers to repository [accnt-id.dkr.ecr.eu-west-1.amazonaws.com/dogs]
....
```

If you now go into the ECR dogs repo, what do you see? \


![Image is pushed to ECR](<../../../../.gitbook/assets/image (376).png>)

### Push to Cats repo

Repeat the same steps for cats.&#x20;

![End result.](<../../../../.gitbook/assets/image (180).png>)

