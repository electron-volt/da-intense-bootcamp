# Pipeline execution

## First execution&#x20;

My first execution failed because Pipeline didn't have the necessary permissions. I didn't change anything, just retried and the second time it worked.&#x20;

After a successful execution, the deploy stage looks like this:

![success](<../../.gitbook/assets/image (327).png>)

Click on the Details link.&#x20;

![cluster has running tasks](<../../.gitbook/assets/image (316).png>)

Now our ECS service has two running tasks, since it has an image that it can use in its task definition!&#x20;

## Testing

Let's go back to the Cloud9 IDE and see if our Application load balancer, listener, target group combo is working. Run this please - but put your own name on line 7

```
save_var STAGING_ALB_URL $( \
  aws cloudformation describe-stacks \
  --stack-name staging-cluster \
  --query "Stacks[0].Outputs[?OutputKey == 'ExternalUrl'].OutputValue" \
  --output text \
)
curl $STAGING_ALB_URL/hello/<your name here>
```

![it works](<../../.gitbook/assets/image (365).png>)

## Push a change

While still in the Cloud9 IDE, let's make a change to `hello-server/server.js` and change the greeting we just saw.&#x20;

Open the file like this:

![opening server.js in our IDE](<../../.gitbook/assets/image (284).png>)

Make a change on line 6. Replace the "hi there" with anything. When you are done, save your changes and run the following commands:

```
cd ~/environment/hello-server
git add -u
git commit -m "Changed greeting"
git push origin master
```

Quick! Go back to CodePipeline and watch the magic happen!&#x20;

![I missed the source stage](<../../.gitbook/assets/image (63).png>)

Once the pipeline execution has finished, go to Cloud9 and repeat the test we did earlier:

```
curl $STAGING_ALB_URL/hello/name
```

and verify that the greeting has changed.&#x20;

## Status check

If we are running out of time or just too tired to continue, we can end the lab here.&#x20;

In that case, skip to "clean up".&#x20;

Otherwise, proceed to Blue/green.&#x20;

![](<../../.gitbook/assets/image (64).png>)

