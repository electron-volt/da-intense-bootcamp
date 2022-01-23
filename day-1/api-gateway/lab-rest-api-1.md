# LAB: REST API 1

## Lambda proxy integration

First we will see what a proxy integration looks like. Lambda proxy integration is a lightweight, flexible API Gateway API integration type that allows you to integrate an API method – or an entire API – with a Lambda function. Because it's a proxy integration, you can change the Lambda function implementation at any time without needing to redeploy your API.

### Create the Lambda function&#x20;

Create a Lambda function with the following information:

Function name: `GetStartedLambdaProxyIntegration`.

Runtime: Node.js

Permissions:&#x20;

1. Choose or create an execution role
2. Create new role from AWS policy templates
3. Role name: `GetStartedLambdaBasicExecutionRole`.
4. Policy templates: leave blank

Function code: paste in the following and deploy changes.&#x20;

```javascript
'use strict';
console.log('Loading hello world function');
 
exports.handler = async (event) => {
    let name = "you";
    let city = 'World';
    let time = 'day';
    let day = '';
    let responseCode = 200;
    console.log("request: " + JSON.stringify(event));
    
    if (event.queryStringParameters && event.queryStringParameters.name) {
        console.log("Received name: " + event.queryStringParameters.name);
        name = event.queryStringParameters.name;
    }
    
    if (event.queryStringParameters && event.queryStringParameters.city) {
        console.log("Received city: " + event.queryStringParameters.city);
        city = event.queryStringParameters.city;
    }
    
    if (event.headers && event.headers['day']) {
        console.log("Received day: " + event.headers.day);
        day = event.headers.day;
    }
    
    if (event.body) {
        let body = JSON.parse(event.body)
        if (body.time) 
            time = body.time;
    }
 
    let greeting = `Good ${time}, ${name} of ${city}.`;
    if (day) greeting += ` Happy ${day}!`;

    let responseBody = {
        message: greeting,
        input: event
    };
    
    let response = {
        statusCode: responseCode,
        headers: {
            "x-custom-header" : "my custom header value"
        },
        body: JSON.stringify(responseBody)
    };
    console.log("response: " + JSON.stringify(response))
    return response;
};
```

### Create the REST API&#x20;

Let's navigate back to API Gateway.&#x20;

* [ ] Click **Create API**&#x20;
* [ ] Go to REST API and click **build**
* [ ] Protocol is REST. We want a new API. Give it the name `LambdaSimpleProxy`
* [ ] Endpoint type is regional. Click **create API.**&#x20;

### Create the resource

Next we will create a resource in our API.

![](<../../.gitbook/assets/image (234).png>)

With the root resource / highlighted, click Actions and choose Create Resource.&#x20;

Proceed with these settings:

Type in resource name helloworld. Leave all checkboxes unchecked. Click create resource.&#x20;

### Create the method

In a proxy integration, the entire request is sent to the backend Lambda function as-is, via a catch-all `ANY` method that represents any HTTP method. The actual HTTP method is specified by the client at run time. The `ANY` method allows you to use a single API method setup for all of the supported HTTP methods: `DELETE`, `GET`, `HEAD`, `OPTIONS`, `PATCH`, `POST`, and `PUT`.&#x20;

Let's create the ANY method:

With the /helloworld resource highlighted, click Actions and Create Method

![](<../../.gitbook/assets/image (231).png>)

Choose the method ANY from the dropdown and click the checkmark next to it to confirm:

![](<../../.gitbook/assets/image (204).png>)

Once the checkmark has been pressed, you can finish the setup with these settings:

Integration type: Lambda function

Use Lambda proxy integration: check

Region: the same one where you created your Lambda function

Lambda: GetStartedLambdaProxyIntegration

Default timeout

Save.

You will be asked to give your Lambda function API Gateway permissions, click OK.&#x20;

### Deploy the API&#x20;

Let's go back to the familiar Actions button and under API actions choose deploy. &#x20;

Choose New Stage, give your stage a name (beta/test/whatever) and deploy.&#x20;

### Test the API&#x20;

We will test the API with a simple GET request via the browser. Copy the invoke URL of your API and then add /\<name of your stage>?name=\<your name>\&city=\<some city> to the end. For example

{% embed url="https://abc123efd.execute-api.us-east-1.amazonaws.com/beta/helloworld?city=Longyearbyen&name=Cecilia" %}

The response will be JSON that starts with&#x20;

```
"message":"Good day, name of city."
```

A little clumsy, I know. We'll improve on this in the next lab. &#x20;

### End result

We made an API with a Lambda backend. Since we did a proxy integration, we get the following:

![proxy integration](<../../.gitbook/assets/image (258).png>)

With this type of integration whatever the Lambda returns is also returned to the client as-is. Next we will look at a non-proxy integration.&#x20;
