# Static web hosting

## Host web app in Amplify&#x20;

We are going to use AWS Amplify to host the code we just pushed into CodeCommit.&#x20;

Navigate to AWS Amplify.&#x20;

Click get started.&#x20;

Choose Host your web app.&#x20;

![](<../../.gitbook/assets/image (170).png>)

In "From your existing code", choose CodeCommit

In "Add repository branch", pick the wildrydes-site repo and master branch.&#x20;

![what is a monorepo?](<../../.gitbook/assets/image (243).png>)

Check the box for "Allow AWS Amplify to automatically deploy all files.."&#x20;

Click Next, then Save and deploy.&#x20;

Wait while Amplify creates the web app.&#x20;

![steady as she goes](<../../.gitbook/assets/image (128).png>)

When the deployment is done, click the https://master...amplifyapp.com link to view the site!&#x20;

## Modify the site

Let's see what happens if we want to make a change to our app.

In the Cloud9 IDE, make a change to index.html: change the title of the site to \<title>Wild Rydes - Rydes of the future!\</title>

Then&#x20;

`git add index.html`

`git commit -m "changed title"`&#x20;

`git push`&#x20;

The app will go from this

![](<../../.gitbook/assets/image (187).png>)

To this:

![](<../../.gitbook/assets/image (132).png>)

## Status

We have our web application up and running. We've seen how easy it is to host web apps in Amplify and how changes made to our repo are immediately deployed in the app.&#x20;
