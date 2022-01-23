# Add review and test

Next we will add an approval action to a stage at the point where you want the pipeline execution to stop so we can manually approve or reject the action.&#x20;

Manual approvals are useful to have someone else review a change before deployment. If the action is approved, the pipeline execution resumes. If the action is rejected—or if no one approves or rejects the action within seven days—the result is the same as the action failing, and the pipeline execution does not continue.

What we'll do:

* Add a review stage to your pipeline
* Make a change to our GitHub repo
* Manually approve a change before it is deployed.

## Add review stage

Go to CodePipeline and click on the pipeline we created.&#x20;

1. Click the **white "Edit" button** near the top of the page.
2. Click the **white "Add stage"** button between the "Build" and "Deploy" stages.
3. In the "Stage name" field type **"Review."**
4. Click the **orange "Add stage" button**.
5. In the "Review" stage, click the **white "Add action group" button**.
6. Under "Action name" type **"Manual\_Review."**
7. From the "Action provider" select **"Manual approval."**
8. Click the **orange "Done" button**.
9. Click the **orange "Save" button** to confirm the changes. You will now see your pipeline with four stages: "Source," "Build," "Review," and "Deploy."

![Adding a stage](<../../../.gitbook/assets/image (352).png>)

## Push a change to the repository

Make changes to app.js in the GitHub repository.&#x20;

It's enough to change the text on line 5. If you are curious, then more info about Express.js can be found here: [https://www.guru99.com/node-js-express.html](https://www.guru99.com/node-js-express.html).

Once the changes have been pushed to the repo, go back to AWS CodePipeline.&#x20;

## Approve the review

I made a commit with message "changes to app.js" and pushed it to GitHub.&#x20;

Here is what is happening over at CodePipeline:

![Waiting for manual approval](<../../../.gitbook/assets/image (221).png>)

The link 54503356 is pointing to GitHub, where there are more details about the commit. The numbers come from the commit ID:

![Vieweing changes in Github](<../../../.gitbook/assets/image (392).png>)

Back in CodePipeline, press the white Review button. A dialogue opens:



![Review](<../../../.gitbook/assets/image (23).png>)

Press Approve. The wheels are turning:

![Pipeline moves to the next stage](<../../../.gitbook/assets/image (126).png>)

Eventually the pipeline is complete.&#x20;

## View the changes

Go to Elastic Beanstalk and find the URL of your web app. Open it and marvel at the changes that have occurred:

![](<../../../.gitbook/assets/image (369).png>)
