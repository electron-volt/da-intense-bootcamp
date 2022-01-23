# LAB: Cloud9

## Create an environment

Navigate to Cloud9 in the AWS console.&#x20;

Click on **Create environment**

Give it a name.

For Environment settings, choose **create a new no-ingress EC2 instance**

Instance type: pick the free tier eligible instance

Platform: Amazon Linux 2

Cost savings: have AWS terminate your instance after 30 minutes.&#x20;

Choose **next** and **create environment**.

### Systems Manager Session Manager

We have not discussed SMSM yet, but if you plan to work with EC2 instances in the future, this is a very useful option. It allows you to access your EC2 instance from a browser, no SSH needed.&#x20;

You can read up on it here:&#x20;

[https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/session-manager.html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/session-manager.html)

## Basic tour

Once your environment is ready, we will take it for a little test drive.&#x20;

In the terminal window on the bottom of the screen, type in&#x20;

```
npm install readline-sync
```

like so:

![every hacking scene in Hollywood](<../../../.gitbook/assets/image (334).png>)

### New file

Make a new file. Paste this in:

{% code title="hello-cloud9.js" %}
```javascript
var readline = require('readline-sync'); 
var i = 10; 
var input;

console.log("Hello Cloud9!"); 
console.log("i is " + i);
do { 
    input = readline.question("Enter a number (or 'q' to quit): "); 
    if (input === 'q') { 
        console.log('OK, exiting.')
    } 
    else { 
        i += Number(input); 
        console.log("i is now " + i); 
    } 
} while (input != 'q');

console.log("Goodbye!");
```
{% endcode %}

I know, super interesting code. Also bad things happen if the input is NaN. Still: Save the file as hello-cloud9.js.

### Run the program&#x20;

There is a button to Run your program, click it:

![run ](<../../../.gitbook/assets/image (6).png>)

### Debug

If you click the margin next to line 10, a red dot appears - a breakpoint.

Also notice the little i that says we are missing a semicolon on line 10?&#x20;

![](<../../../.gitbook/assets/image (218).png>)

On the right hand side panel, you'll see a bug icon - click it to open the debugger.&#x20;

#### Watch expression&#x20;

In the debug window, under Watch expressions type "input":

![watch expression: input](<../../../.gitbook/assets/image (390).png>)

Next:

1. Run the code again&#x20;
2. Enter any number in the terminal&#x20;
3. Press enter

Execution has stopped:

![we are stuck here](<../../../.gitbook/assets/image (49).png>)

In the Debugger window, there is now a blue arrow ("Play") which allows you to resume:

![Blue arrow](<../../../.gitbook/assets/image (248).png>)

To stop the debugger, click the red square in the terminal window.

![Red Square](<../../../.gitbook/assets/image (17).png>)

## Clean up&#x20;

At the top left hand corner, click the cloud icon and choose Go to your dashboard.

![](<../../../.gitbook/assets/image (227).png>)

Then click the Delete button. This terminates the Cloud9 environment.&#x20;
