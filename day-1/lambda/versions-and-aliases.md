# Versions and aliases

## Lambda ARN's

Every AWS resource has an ARN. For your Lambda function, it will be&#x20;

**arn:aws:lambda:<**_**region**_**>:<**_**account ID**_**>:function:<**_**name of function**_**>**

and all the versions will have a version suffix like

**arn:aws:lambda:us-west-1:123123123123:function:MyLambdaFunction:1.**

An ARN without a version number is called an unqualified ARN and one with a version number is called a qualified ARN.

### Invoking your function

If you invoke your function with the unqualified ARN, then the $LATEST is invoked. If you want to invoke a specific version, you use the qualified ARN.&#x20;

Seems a bit laborious, right? You have to memorize that the production version of the ConsoleLambda function was version 2 and then remember to put a :2 at the end of the ARN. Then you make changes and now the new production version is version 3.. if only there was a better way!&#x20;

## Aliases

In AWS' own words,&#x20;

**A Lambda alias is like a pointer to a specific function version.**&#x20;

You are able to publish a version of your function and call it "prod". This way you don't need to keep track of version numbers and qualified ARN's. When you make changes to the production Lambda, you can publish a new version and set the alias "prod" to point to the new version. Customers will always invoke "prod" and not care about which version number it is.&#x20;

Let's go ahead and give our Lambda a new alias!&#x20;
