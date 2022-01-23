# LAB: HTTP API

We are going to build a serverless API. We will create an HTTP API and a Lambda function. When we invoke the HTTP API, API Gateway routes the request to our Lambda function. Lambda runs the Lambda function and returns a response to API Gateway. API Gateway then returns a response to us.

## Create the Lambda function

* [ ] Create a Lambda function.&#x20;
* [ ] Choose "author from scratch".
* [ ] Name: api-lambda
* [ ] Runtime: Node.js.&#x20;
* [ ] Permissions: go with default settings.&#x20;

You can change the message that the function returns to be anything you want, like "This is my first serverless api!"

## Create the HTTP API&#x20;

We will create an HTTP API.&#x20;

* [ ] Navigate to API Gateway
* [ ] Choose HTTP API & Build
* [ ] Add integration&#x20;
* [ ] For type, choose Lambda. Select your api-lambda function.&#x20;
* [ ] Name your API **my-serverless-api**
* [ ] Go with default settings for both route and stage
* [ ] Create API

Once your API is created, you will see an overview of its details. It will have an API ID, a URL, a stage called $default and so on.&#x20;

## Test the API&#x20;

We will test the API through our browser. If you click on the invoke URL of your API as it is, it will return &#x20;

```
{"message":"Not Found"}
```

Let's instead add the name of our function api-lambda to the end of the URL:

[https://api-id.execute-api.aws-region.amazonaws.com/api-lambda](https://gibberish.execute-api.aws-region.amazonaws.com/api-lambda)

Now it should return the message that you customized in your Lambda function.&#x20;
