---
description: Shortest lab ever
---

# LAB: Install the AWS CLI

## Install the AWS CLI&#x20;

We have created a user account with two types of access to AWS:

* console access to the AWS console over a browser with a username, password and MFA device
* programmatic access with access keys

Next we'll install the AWS command line interface or CLI tool that will allow you to manage your AWS resources from the command line.

Installers for Windows, Linux and macOS can be found here [https://aws.amazon.com/cli/](https://aws.amazon.com/cli/)

Follow the instructions for your OS.&#x20;

### Verify the installation

Verify that the CLI tool was installed by running&#x20;

`aws --version`&#x20;

## Configure credentials&#x20;

Once you have the CLI tool installed, you can configure your programmatic access credentials. This is the access key ID and secret access key pair that was shown after your user was created. Note: we do not want to generate access keys for the root user, but use the access keys of the mere mortal admin user that we created previously.

Two ways of going about it:

### Import from the CSV file

If you downloaded the access keys as a .csv, use it to import your credentials:

`aws configure import --csv file://credentials.csv`

### Use aws configure&#x20;

You can also use `$ aws configure` to put in your credentials as well as choose a default region and output format. This command updates the `credentials` and `config` files. Example:

`$ aws configure`&#x20;

`AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE`

`AWS Secret Access Key [None]: wJalrXUtnFUMI/K7MDENG/bPxRfiCYEXAMPLEKEY`&#x20;

`Default region name [None]: pick a region`

`Default output format [None]: yaml`

More details can be found here [https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html)

🎯 Note: you can pick whichever region you like. Stockholm region eu-north-1 is a good choice, most of the labs in this course have been designed and tested there.&#x20;

### Test your configuration

You can test your configuration by attempting to list all of your s3 buckets (we don't have any at this point and you do not have to know what an s3 bucket is):

`$ aws s3 ls`

This command lists (ls) all of your S3 (s3) buckets.&#x20;

## End result

You should have the AWS CLI installed and configured correctly.&#x20;
