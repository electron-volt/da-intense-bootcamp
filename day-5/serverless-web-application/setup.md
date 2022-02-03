# Setup

## Region&#x20;

Choose one of these regions to build this lab in and put all of your resources there:

* us-west-2 (US West - Oregon)
* us-east-2 (US East - Ohio)
* us-east-1 (US East - Northern Virginia)
* eu-central-1 (Europe - Frankfurt)&#x20;

## Git credentials&#x20;

You will need the HTTPS git credentials we created on day 4, in the CodeCommit lab.&#x20;

If you do not have access to IAM credentials, then please use a combination of Cloudshell and Git-remote-commit. Instructions are in the CodeCommit lab from day 4.&#x20;

## Cloud9 IDE

Create a Cloud9 IDE in your chosen region and open the terminal. Note: if you are using CloudShell due to git-remote-commit, then pick a region where CloudShell is supported.&#x20;

## Set up our Git repository

### Create Git repository

We will use CodeCommit to create a Git repository.

Create a repository called **wildrydes-site**.

In the Cloud9 terminal, clone the repo with the HTTPS URL.

### Populate the repository

On your local machine or in the Cloud9 terminal, change directory into the wildrydes-site   run the commands below.

These commands change directory into your repository and copy some static files from a public S3 bucket. Then push them into your repo.

```
aws s3 cp s3://wildrydes-us-east-1/WebApplication/1_StaticWebHosting/website ./ --recursive
git add .
git commit -m 'initial'
git push
```

Here is what it looks like now in the repo:

![](<../../.gitbook/assets/image (247).png>)

And the terminal:

![](<../../.gitbook/assets/image (288).png>)
