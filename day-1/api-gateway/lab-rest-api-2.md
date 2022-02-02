# LAB: REST API 2

## Lambda non-proxy integration

In this lab we are going to create a Lambda function (`GetStartedLambdaIntegration`) and expose the function through an API Gateway API with a generic HTTP method.&#x20;

* We use the request body, a URL path variable, a query string, and a header to receive required input data from the client.&#x20;
* We turn on the API Gateway request validator for the API to ensure that all of the required data is properly defined and specified.&#x20;
* We configure a mapping template for API Gateway to transform the client-supplied request data into the valid format as required by the backend Lambda function.

Phew, a lot of work.

### Create the Lambda

#### Create the function

Name: `GetStartedLambdaIntegration`

Runtime: Node.js&#x20;

Permissions:

1. Create a new role from AWS policy templates
2. Name: GetStartedLambdaIntegrationRole
3. Use simple microservices permissions

Code:

```javascript
'use strict';
var days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];            
var times = ['morning', 'afternoon', 'evening', 'night', 'day'];

console.log('Loading function');

exports.handler = function(event, context, callback) {
  // Parse the input for the name, city, time and day property values
  let name = event.name === undefined ? 'you' : event.name;
  let city = event.city === undefined ? 'World' : event.city;
  let time = times.indexOf(event.time)<0 ? 'day' : event.time;
  let day = days.indexOf(event.day)<0 ? null : event.day;

  // Generate a greeting
  let greeting = 'Good ' + time + ', ' + name + ' of ' + city + '. ';
  if (day) greeting += 'Happy ' + day + '!';
  
  // Log the greeting to CloudWatch
  console.log('Hello: ', greeting);
  
  // Return a greeting to the caller
  callback(null, {
      "greeting": greeting
  }); 
};
```

#### Test the function

Create a test event. The name of the test event can be anything. The contents should be as follows:

```
{ "name": "Bruce", "city": "Gotham", "time": "night", "day": "Saturday" }
```

You should get this response:

```
{ "greeting": "Good night, Bruce of Gotham. Happy Saturday!" }
```

### Create the API&#x20;

1. Go to API Gateway.&#x20;
2. Click Create API
3. Under REST API, click build.
   1. there are two kinds of REST API's. We want the one that is **not** private.
4. We want a REST API, new API&#x20;
5. Give it a name **lambda-integration**
6. Endpoint type is regional
7. &#x20;Create API

#### Create the resource

1. With the root resource highlighted, click _Actions_ and create resource.&#x20;
2. Leave proxy resource unchecked
3. Resource name: city&#x20;
4. Resource path: {city}
5. Check Enable API gateway CORS

#### Create the method

1. With the new resource city highlighted, click **Actions** and **create method**
2. For method choose ANY
3. Click the checkmark
4. For method integration, choose Lambda
5. Leave proxy integration unchecked
6. Check you are in the right region
7. Choose the **GetStartedLambdaIntegration**
8. Default timeout is OK&#x20;
9. When prompted, click OK to give Lambda API Gateway permissions.&#x20;

Now we move on to the method execution. On your screen you will see boxes representing the different requests and responses.&#x20;

#### Method request

1. Click on Method request
2. Expand the **URL Query String Parameters** section. Choose **Add query string**. Type `time` for **Name**. Select the **Required** option and choose the check-mark icon to save the setting. Leave **Caching** cleared to avoid an unnecessary charge for this exercise.
3. Expand the **HTTP Request Headers** section. Choose **Add header**. Type `day` for **Name**. Select the **Required** option and choose the check-mark icon to save the setting. Leave **Caching** cleared to avoid an unnecessary charge for this exercise.

Next we need to define the method request payload.&#x20;

1. On the left hand side of the screen, click Models
2. Click Create
3. Name: **GetStartedLambdaIntegrationUserInput**
4. Content type: application/json
5. Leave description blank&#x20;
6. Copy the following into the Model schema editor, then click create model.

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "title": "GetStartedLambdaIntegrationInputModel",
  "type": "object",
  "properties": {
    "callerName": { "type": "string" }
  }
}
```

#### Integration request

1. Go back into Resources
2. Click the ANY method of the city resource
3. Click Integration request
4. At the bottom you see Mapping templates, expand it&#x20;
5. Click add template
6. Content type is application/json
7. Click the checkmark
8. You will be asked to secure the integration. Click "yes, secure this integration"&#x20;
9. The radio button for "When there are no templates defined" should be selected
10. From the "generate template" dropdown, choose the model we created: GetStartedLambdaIntegrationUserInput
11. Replace the mapping script with the following and click save.

```
#set($inputRoot = $input.path('$'))
{
  "city": "$input.params('city')",
  "time": "$input.params('time')",
  "day":  "$input.params('day')",
  "name": "$inputRoot.callerName"
}
```

We will not configure integration or method response.&#x20;

### Test the method

The method execution view has a test option:

![Test](<../../.gitbook/assets/image (305).png>)

Click on the TEST.

1. Method is POST
2. Path is Gotham
3. Query string is time=night
4. Headers is day:Saturday
5. Request body is { "callerName":"Bruce" }
6. hit Test

The output should contain these lines, among others:

```
Endpoint response body before transformations: {"greeting":"Good night, Bruce of Gotham. Happy Saturday!"}
Method response body after transformations: {"greeting":"Good night, Bruce of Gotham. Happy Saturday!"}
```

This is the equivalent of the following HTTP request:

```http
POST /Gotham?time=night
day:Saturday

{
    "callerName": "Bruce"
}
```

### Deploy the API&#x20;

The test invocation is a simulation and has limitations. For example, it bypasses any authorization mechanism enacted on the API. To test the API execution in real time, you must deploy the API first. To deploy an API, you create a **stage** to create a snapshot of the API at that time. The stage name also defines the base path after the API's default host name. The API's root resource is appended after the stage name. When you modify the API, you must redeploy it to a new or existing stage before the changes take effect.

1. In the Actions dropdown, choose Deploy API&#x20;
2. Choose new stage
3. Give the stage the name **test**
4. Descriptions can be blank
5. Deploy

### Test the API

You can now test the API using for example cURL. Postman, Visual Studio Code work too.

Just replace the relevant fields:

```
curl -v -X POST \
  'https://<your api id>.execute-api.region.amazonaws.com/test/Gotham?time=night \
  -H 'content-type: application/json' \
  -H 'day: Saturday' \
  -H 'x-amz-docs-region:<your chosen aws region>' \
  -d '{
	"callerName": "Bruce"
}'
```
