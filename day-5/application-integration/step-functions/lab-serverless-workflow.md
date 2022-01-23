# LAB: Serverless Workflow

In this tutorial, you will use Step Functions to orchestrate a serverless workflow in response to a CloudWatch event.&#x20;

First, you’ll create a very simple state machine using AWS Step Functions.&#x20;

Then, you’ll use Amazon CloudWatch Events to set up a rule that runs your state machine every minute.

Then we will modify the state machine so that it passes as output the result of the state.&#x20;

## Create an IAM role

We need to give Step Functions some permissions, so let's head over to IAM. This role will be used in this lab and the next lab too.&#x20;

Go to **IAM > roles > create role**

On the **Select type of trusted entity page**, under AWS service, select **Step Functions** from the list, and then choose **Next: Permissions**.

AWS will automatically add the necessary Lambda policy. On the **Attach permissions policy** page, choose **Next: Review**.

On the **Review** page, type _step\_functions\_basic\_execution_ for **Role name** and click **Create role**.

## Create the state machine

Navigate to the Step Functions console. On the left hand side panel, click **State machines**.

Click **Create state machine**.

Choose **Write your workflow in code**.

Type: standard

In Definition, paste in the following:

```json
{
  "Comment": "A simple workflow example of how Amazon States Language works, using a Pass state",
  "StartAt": "Do Something",
  "States": {
      "Do Something": {
          "Type": "Pass",
          "Result": "Work complete!",
          "End": true
      }
  }
}
```

Take a moment to view the code and visual representation of the state machine. Then click **next**.

Name: _ScheduledWorkflow_

Permissions: choose existing role and pick _step\_functions\_basic\_execution._

Skip logging, tracing and tags. Click **create state machine**.

## EventBridge

Now that you have created your state machine, you can create an Amazon CloudWatch Events rule that runs your state machine according to a schedule.

Navigate to EventBridge.&#x20;

Click **create rule**.

Name: _test-rule_

Pattern:

* schedule
* Fixed rate every 1 minutes

Use AWS default event bus.&#x20;

Target: Step Functions state machine. Choose ScheduledWorkflow.

Create a new role for this specific resource.

**Create**.

## Monitor executions

Wait for a few minutes to give EventBridge time to fire the rule. Let's go back to Step Functions, click on our state machine and see if it has been executed.&#x20;

![Executions](<../../../.gitbook/assets/image (214).png>)

Click on a name of an execution to view details.

![super complex](<../../../.gitbook/assets/image (150).png>)

## Modify the state machine

We will make a slight modification to our state machine.&#x20;

Go to Step Functions, select your workflow and click **edit**.

Replace the previous code with this:

```json
{
  "Comment": "A simple workflow example of how Amazon States Language works, using a Pass state",
  "StartAt": "Do Something",
  "States": {
      "Do Something": {
          "Type": "Pass",
          "Result": "Work complete!",
          "ResultPath": "$.taskresult",
          "End": true
      }
  }
}
```

Only line 8 has changed. What are we doing here?&#x20;

The Pass state simply passes its input to its output. We can use the ResultPath field to modify the output of a state. For example, you can replace the state input with the result it produces (for example, output from a Task state’s Lambda function).

On line 8 we combine the state’s input with its result. Click **save**.

## Executions&#x20;

Before the modification:

![output with no mention of EventBridge](<../../../.gitbook/assets/image (158).png>)

After the modification:

![Output has results from EventBridge](<../../../.gitbook/assets/image (94).png>)

## Clean up&#x20;

We can delete

* the EventBridge rule
* the state machine

Keep the IAM role as we will use it in the next lab.&#x20;
