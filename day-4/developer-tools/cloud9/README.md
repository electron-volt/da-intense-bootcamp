# Cloud9

## How it works

Cloud9 is an IDE that you can use via the web browser.&#x20;

Here's how it works:

* your source code can be in CodeCommit or some other remote repository
* your environment can run on an EC2 instance or on your own server
* you access the IDE on your local machine through a browser.

## Environments

Behind the scenes, there are a couple of ways you can connect your environments to computing resources:

* You can instruct AWS Cloud9 to create an Amazon EC2 instance, and then connect the environment to that newly created EC2 instance. This type of setup is called an _EC2 environment_.
* You can instruct AWS Cloud9 to connect an environment to an existing cloud compute instance or to your own server. This type of setup is called an _SSH environment_.
