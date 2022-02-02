# LAB: Create Git repository

In this lab we will create a Git repository and push some changes into it. If you don't know git, no worries - you'll learn everything you need as we go along.&#x20;

We will&#x20;

* Set up the credentials that we need for CodeCommit and Git
* Create a repository
* Clone it
* Push some content into our repo.&#x20;

Windows users: I recommend Git Bash for this lab.&#x20;

## Setting up access&#x20;

### Option 1: Set up HTTPS Git credentials for CodeCommit

We will create credentials for our admin user. Just make sure that you are signed in as the user that you intend to create credentials for.&#x20;

Go to **IAM > Users** and select your user.&#x20;

On the user details page, choose the **Security Credentials** tab, and in **HTTPS Git credentials for AWS CodeCommit**, choose **Generate**.

![Generate credentials](<../../../.gitbook/assets/image (79).png>)

Be sure to download your credentials or store them in a password manager. You cannot choose your own username and **you cannot view the password later.**&#x20;

### **Option 2: Git-remote-commit**

If for some reason you can't access your IAM user permissions, then you can use git-remote-commit. It works well when used inside AWS CloudShell.&#x20;

Follow these instructions:

{% embed url="https://github.com/aws/git-remote-codecommit" %}

## Create the Git repository&#x20;

Head over to **CodeCommit**.

Click **create repository.**

Give the repo a name and click **create.**

That's it!&#x20;

## Clone the repository

### Opion 1: If you have the Git credentials&#x20;

From the console, get the HTTPS URL that you need to clone your repository;

![pick HTTPS](<../../../.gitbook/assets/image (307).png>)

Then in a terminal, run the following command and ignore the warning. Username and password are the IAM credentials we created:

```
git clone https://git-codecommit.region.amazonaws.com/v1/repos/name
Cloning into 'name'...
Username for ...: *****
Password for ...: ****
warning: You appear to have cloned an empty repository.
```

Optional - set main as the default branch:

```
git config --global init.defaultBranch main
```

Otherwise the default branch will be called master.&#x20;

### Option 2: GRC

You can find the clone URL from the console

![GRC URL ](<../../../.gitbook/assets/image (140).png>)

For my repo, which is called test-repo the command is&#x20;

```
git clone codecommit::eu-west-1://test-repo
```

## First commit

Run git config to set up your username and email:

```
git config --local user.name "your-user-name" 
git config --local user.email your-email-address
```

Change into the directory where your repository was cloned. Create two text files on your machine, `foo.txt` and `bar.txt.`&#x20;

Run git add&#x20;

```
git add foo.txt bar.txt
```

Optional: run the useful and informative

```
git status
```

Then git commit:

```
git commit -m "Added two text files."
```

## Push&#x20;

We have made a commit, but we have yet to push the changes to the repo. If your default branch is main, use this:

```
git push -u origin main
```

Else, just git push.&#x20;

Your best friend, git status:

```
git status
```

![good luck finding a good git meme. ](<../../../.gitbook/assets/image (277).png>)

## Clean up

AWS CodeCommit is very generous with its free tier offering, so this repo won't really cost us anything. We won't be needing it though, so go ahead and delete it.

That's it for CodeCommit, although it will resurface in our CodeBuild lab.
