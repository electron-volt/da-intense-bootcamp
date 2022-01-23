# CodeCommit

CodeCommit is a secure, highly scalable, managed source control service that hosts private Git repositories. You can use CodeCommit to store anything from code to binaries.

With CodeCommit, you can:

* **Benefit from a fully managed service hosted by AWS**. There is no hardware to provision and scale and no server software to install, configure, and update.
* **Store your code securely**. CodeCommit repositories are encrypted at rest as well as in transit.
* **Store anything, anytime**. CodeCommit has no limit on the size of your repositories or on the file types you can store.
* **Integrate with other AWS and third-party services**.&#x20;
* **Easily migrate files from other remote repositories**. You can migrate to CodeCommit from any Git-based repository.

## Git credentials

There are different kinds of git credentials that you will need in order to use CodeCommit. They depend on how you want to connect: over SSH or HTTPS.

### Over SSH

Key pairs. Can be used with or without AWS CLI.&#x20;

### Over HTTPS

This is the option that we will be using in our labs.&#x20;

In IAM, you can create HTTPS Git credentials for AWS CodeCommit:

![IAM > users > permissions](<../../../.gitbook/assets/image (96).png>)

We will complete this step in our next lab.&#x20;
