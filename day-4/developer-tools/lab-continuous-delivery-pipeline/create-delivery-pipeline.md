# Create delivery pipeline

In this module, you will use AWS CodePipeline to set up a continuous delivery pipeline with source, build, and deploy stages. The pipeline will&#x20;

* detect changes in the code stored in your GitHub repository
* build the source code using AWS CodeBuild
* then deploy your application to AWS Elastic Beanstalk.

Our app.js from GitHub should eventually be shown in our Elastic Beanstalk web app.

What we'll do:

* Set up a continuous delivery pipeline on AWS CodePipeline
* Configure a source stage using your GitHub repo
* Configure a build stage using AWS CodeBuild
* Configure a deploy stage using your AWS ElasticBeanstalk application
* Deploy the application hosted on GitHub to ElasticBeanstalk through a pipeline.

## Create Pipeline

### Pipeline

1. In a browser window, open the AWS CodePipeline Console.
2. Click the **orange "Create pipeline" button**. A new screen will open up so you can set up the pipeline.
3. In the "Pipeline name" field type **"Pipeline-DevOpsGettingStarted."**
4. Visually confirm that **"New service role"** is selected.
5. Click the **orange "Next" button**.

### Source

1. Select **"GitHub"** from the "Source provider" dropdown menu.&#x20;
   1. Pick **Version 1** and ignore the warning. We will continue with OAuth for this lab.&#x20;
2. Click the **white "Connect to GitHub" button**. A new browser tab will open asking you to give AWS CodePipeline access to your GitHub repo.
3. Click the **green "Authorize aws-codesuite" button**. Next, you will see a green box with the message "You have successfully configured the action with the provider."
4. From the "Repository" dropdown **select the repo you created in Module 1**.
5. Select **"main"** from the "branch" dropdown menu.
6. Visually confirm that **"GitHub webhooks"** is selected.
7. Click the **orange "Next" button**.

### Build stage

1. From "Build provider dropdown", select "**AWS CodeBuild"**
2. Check that you are in your usual region&#x20;
3. Select your CodeBuild project
4. Click N**ext**.

### Deploy stage

1. Select **"AWS ElasticBeanstalk"** from the "Deploy provider" dropdown menu.
2. Check that you are in your usual region&#x20;
3. Click the field under "Application name" and confirm you can see the Elastic Beanstalk app created in Module 2.
4. Select the correspoding environment from the "Environment name" text box.
5. Click the **orange "Next" button**. You will now see a page where you can review the pipeline configuration.

### Review

Review your settings and then create the pipeline.&#x20;

![you have a chance to review ](<../../../.gitbook/assets/image (124).png>)

## Successful execution&#x20;

The pipeline should execute successfully, with similar boxes for all stages:\


![success at the source ](<../../../.gitbook/assets/image (89).png>)

## Elastic Beanstalk

Now let's go to Elbe and see what our app looks like now.&#x20;

![Elastic Beanstalk is now serving this](<../../../.gitbook/assets/image (378).png>)

You should see a white background and whatever text you used to modify the line 5 of app.js in our first commit to our repo.&#x20;

## Where we're at

![architecture now](<../../../.gitbook/assets/image (236).png>)

We have created a continuous delivery pipeline on AWS CodePipeline with three stages: source, build, and deploy.&#x20;

* The source code from the GitHub repo created in Module 1 is part of the source stage.&#x20;
* That source code is then built by AWS CodeBuild in the build stage.&#x20;
* Finally, the built code is deployed to the AWS Elastic Beanstalk environment created in Module 3.
