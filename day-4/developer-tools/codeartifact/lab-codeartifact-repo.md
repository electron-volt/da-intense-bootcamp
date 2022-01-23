# LAB: CodeArtifact repo

## Walk-through

In this lab we are going to create a CodeArtifact repository.

This lab assumes that you have a package manager client like pip, twine, mvn or npm installed on your computer. If you don't have any and don't want to install one, then you can just read through the lab without doing the steps yourself.&#x20;

The instructions are written for Python, but use whatever you like.&#x20;

## Create the repository

Navigate to CodeArtifact.&#x20;

Click **create repository**.

Give it a suitable name like my-repo.

Description can be left blank.&#x20;

For public upstream repositories, I am going to use pypi-store.

Click **next**.

### Domain&#x20;

Under Domain, for AWS account pick "this AWS account".&#x20;

We have to create a domain as we don't have one - and each repo belongs to a domain.&#x20;

Give your domain a name like my-domain.&#x20;

### Encryption&#x20;

We have to use encryption. We will pick AWS managed key in KMS settings (more about KMS on day 5).&#x20;

Click **next**.

### Review

Review the settings and then click **create repository.**

## Connection instructions

When your repo has been created, you will be taken to this page:

![my-repo](<../../../.gitbook/assets/image (293).png>)

Click on **View connection instructions**.

This dialogue opens up. Choose pip from the dropdown to reveal the CLI command.&#x20;

![](<../../../.gitbook/assets/image (321).png>)

Copy the CLI command and run it.&#x20;

You will ideally see this response:

```
Successfully configured pip to use AWS CodeArtifact repository https://eves-domain-123456789.d.codeartifact.eu-north-1.amazonaws.com/pypi/my-repo/ 
Login expires in 12 hours at 2022-01-17 05:24:54+02:00
```

### Install something

While still in your terminal, try installing something. For example `pip install scipy.`&#x20;

It may take a moment, but eventually your CodeArtifact repository will show the newly installed package:

![from here ](<../../../.gitbook/assets/image (37).png>)

![to here.](<../../../.gitbook/assets/image (281).png>)

## Clean up&#x20;

Time to delete things again:

* the repo you created + the upstream repo
* the domain&#x20;

The domain can only be deleted once it has 0 repos in it.&#x20;
