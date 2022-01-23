# RESTful API

In this module you'll use Amazon API Gateway to expose the Lambda function you built in the previous module as a RESTful API. This API will be accessible on the public Internet. It will be secured using the Amazon Cognito user pool you created in the previous module. Using this configuration you will then turn your statically hosted website into a dynamic web application by adding client-side JavaScript that makes AJAX calls to the exposed APIs.

The static website you deployed in the first module already has a page configured to interact with the API you'll build in this module. The page at /ride.html has a simple map-based interface for requesting a unicorn ride. After authenticating using the /signin.html page, your users will be able to select their pickup location by clicking a point on the map and then requesting a ride by choosing the "Request Unicorn" button in the upper right corner.

## Create new REST API&#x20;

Navigate to API Gateway.&#x20;

Click Create API.&#x20;

REST API (not private)

Protocol: REST

Create new API&#x20;

API Name: WildRydes

Endpoint type: edge optimized.

## Create a Cognito user pools authorizer

In your new API, select Authorizers and Create New Authorizer.

![authorizer](<../../.gitbook/assets/image (19).png>)

Name: WildRydes

Type: Cognito

For user pool, pick your pool from the dropdown

For token source, write Authorization.&#x20;

Leave validation empty.&#x20;

Create.

### Test the authorizer

Go back to your WildRydes web app and sign in with your test user credentials.&#x20;

You will see the token, copy it.&#x20;

Go back to API Gateway, the authorizer and click Test.&#x20;

Paste in the token:

![test the authorizer.](<../../.gitbook/assets/image (90).png>)

## Create new resource and method

Create a new resource called /ride within your API (enable CORS).&#x20;

Then create a POST method for that resource and configure it to use a Lambda proxy integration backed by the RequestUnicorn function.

![](<../../.gitbook/assets/image (138).png>)

Hint: resources > Actions > create resource /ride > actions > create method > POST > hit checkmark.

### Add authorizer

Click the Method request card.&#x20;

Click the pen icon next to Authorization. Select WildRydes and then click the checkmark.

![authorization](<../../.gitbook/assets/image (210).png>)

## Deploy your API&#x20;

In your API, go to Actions > Deploy API.&#x20;

For stage, select new stage.&#x20;

Name of the stage: prod.

Then deploy the API.&#x20;

Take note of the invoke URL.

## Update the website config

Back in Cloud9, let's edit js/config.js again.&#x20;

Now we have the invokeUrl as well, so add that value in.&#x20;

Then push changes to repo.
