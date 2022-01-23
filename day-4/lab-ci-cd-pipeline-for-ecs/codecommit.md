# CodeCommit

## Setting up CodeCommit&#x20;

In Cloud9, you can create a new terminal: click on the plus sign and select new terminal.&#x20;

![new terminal in Cloud9 IDE](<../../.gitbook/assets/image (10).png>)

## Configure git&#x20;

### Credential helper

Previously we created Git HTTPS credentials for our admin user in IAM. Let's verify that we have the necessary access by running this command:

```
git config --global --list
```

If the output looks like this, then you are good to go!&#x20;

![let's not start a war over nano vs gedit vs vim](<../../.gitbook/assets/image (4).png>)

If your output doesn't look like that, then run these commands;

```
git config --global credential.helper '!aws codecommit credential-helper $@'
git config --global credential.UseHttpPath true
```

### Username and email

Run these with the correct values:

```
git config --global user.name "put name here"
git config --global user.email you@foo.bar
```

## Test connection - optional&#x20;

This part is optional - we are just testing that we can connect to CodeCommit. Skip this if in a hurry or everyone already knows git.&#x20;

### Make a local test repo (CLI)&#x20;

```
cd ~/environment/ 
mkdir lab-test 
cd lab-test 
git init 
echo "Hello" > hello.txt 
git add . 
git commit -m "Test commit"
```

### Make a CodeCommit repo&#x20;

We have seen how to create a CodeCommit repo via the console. &#x20;

The way to create one via the CLI would be this:

`aws codecommit create-repository --repository-name some name here`&#x20;

If we ran that command, it would create a repository and return some data like the repo URL. However we can save ourselves a few clicks and do the following instead. This creates the repo, gets the clone URL, adds the repo as our origin and pushes our hello.txt to the new repo.&#x20;

```
repo_url=$(aws codecommit create-repository --repository-name lab-test --query repositoryMetadata.cloneUrlHttp --output text)
echo Pushing to $repo_url
git remote add origin $repo_url
git push -u origin master
```

You can check it out in the console:

![repo in console](<../../.gitbook/assets/image (382).png>)

## Create and merge a branch

### Hotfix branch&#x20;

This creates a new branch called hotfix in the local repo:

```
git checkout -b hotfix
```

Then edit the hello.txt file (for example echo "put text here" > hello.txt). Then run&#x20;

```
git commit -a -m "Let me fix that for you"
git push -u origin hotfix
```

In the console we see the new branch:

![branches in console](<../../.gitbook/assets/image (233).png>)

Then merge your changes to master:

```
git checkout master
git merge hotfix
git push origin master
```

In the console you can verify that the changes you made to hello.txt are now shown in the master branch as well.&#x20;

## Clean up

We don't need this test repo for anything, so we are going to delete the CodeCommit repo as well as the directory.&#x20;

```
aws codecommit delete-repository --repository-name lab-test
cd ~/environment
rm -rf lab-test
```
