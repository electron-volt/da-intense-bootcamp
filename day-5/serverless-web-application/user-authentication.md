# User authentication

Next we are going to use Cognito to manage user's accounts.

When users visit the website they will first register a new user account. For the purposes of this workshop we'll only require them to provide an email address and password to register.&#x20;

After users submit their registration, Amazon Cognito will send a confirmation email with a verification code to the address they provided. To confirm their account, users will return to your site and enter their email address and the verification code they received. You can also confirm user accounts using the Amazon Cognito console if want to use fake email addresses for testing.

## Create a Cognito user pool&#x20;

Navigate to Cognito.&#x20;

Click Create user pool&#x20;

![](<../../.gitbook/assets/image (253).png>)

Then fill in these details:

![](<../../.gitbook/assets/image (250).png>)

Click Next to get to password requirements. You can proceed with the Cognito defaults for passwords or choose your own.&#x20;

We do not want MFA for this lab.&#x20;

![](<../../.gitbook/assets/image (127).png>)

![](<../../.gitbook/assets/image (389).png>)

For email, send email with Cognito.&#x20;

User pool name: WildRydes.

![Do not generate a client secret](<../../.gitbook/assets/image (196).png>)

Review and create.

### User pool id and App client id

In AWS Cognito, go to your new user pool. In the overview you will find User pool ID.&#x20;

Then go to tab App integration and scroll all the way down. You will find your Client ID there.&#x20;

![](<../../.gitbook/assets/image (264).png>)

## Modify config.js

In the Cloud9 IDE, navigate to directory js. Using your favourite text editor, open js/config.js.

![initial config.js](<../../.gitbook/assets/image (117).png>)

Replace the placeholder values with real ones.&#x20;

Leave api and invokeUrl empty - this part comes later.&#x20;

Push changes to the git repo.&#x20;

## Validate

Wait while Amplify redeploys your app. Once it is ready, visit the website and add /register.html to the end of the domain name - or press the Giddy up! button.&#x20;

![registration page](<../../.gitbook/assets/image (42).png>)

You can use a fake address like test@foo.bar.&#x20;

### Verify in Cognito

Back in the Cognito console, we see our test user:

![](<../../.gitbook/assets/image (191).png>)

Click the user name, then from actions select confirm account.

![](<../../.gitbook/assets/image (278).png>)

Then go to your-amplify-domain/signin.html and sign in using your fake email and the password you made up.&#x20;

This is a sign of success:

![uuh capitol hill](<../../.gitbook/assets/image (109).png>)

