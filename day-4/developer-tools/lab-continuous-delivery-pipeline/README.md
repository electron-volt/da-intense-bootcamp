# LAB: Continuous delivery pipeline

Let's combine everything we have learned about these developer tools and build something.

We are going to:

* Set up a GitHub repository for our application code
* Create a web app with AWS Elastic Beanstalk
* Configure AWS CodeBuild to build the source code from GitHub
* Use AWS CodePipeline to set up the continuous delivery pipeline with source, build, and deploy stages. The code will be deployed to Elastic Beanstalk.
* Add a manual review stage to the pipeline.

Then whenever changes are made to the repo, we can review the changes and if we accept them, the changes will be deployed to our web application.&#x20;

![Architecture](<../../../.gitbook/assets/image (27).png>)



This tutorial is divided into five parts:

1. Set Up Git Repo using GitHub\

2. Deploy Web App using AWS Elastic Beanstalk.\

3. Create Build Project using AWS CodeBuild.\

4. Create Delivery Pipeline using AWS CodePipeline.\

5. Add a review stage to the pipeline and test its execution.
