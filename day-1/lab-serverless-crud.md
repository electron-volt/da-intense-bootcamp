---
description: Loppuvastus
---

# LAB: Serverless CRUD

We're going to build a simple CRUD or create, read, update, delete application using the serverless technologies that we have learned to use today. Here is a picture:

![serverless CRUD ](<../.gitbook/assets/image (364).png>)

Our database uses id (String) as its primary key (partition key). We use HTTP verbs GET, PUT and DELETE to create, read, update and delete items in the database.&#x20;

The instructions will be very short, as we have covered these steps in other labs.&#x20;

## Create the DynamoDB table

Create a table called **http-crud-tutorial-items.**

Primary key (partition key) is **id** (String).&#x20;

## Create the Lambda function&#x20;

Create a Lambda function.&#x20;

Name: **http-crud-tutorial-function**

Runtime: Node.js

Execution role: create new from templates. Use template **simple microservice permissions**. Name of role is **http-crud-tutorial-role.**

Code:

{% code title="index.js" %}
```javascript
const AWS = require("aws-sdk");

const dynamo = new AWS.DynamoDB.DocumentClient();

exports.handler = async (event, context) => {
  let body;
  let statusCode = 200;
  const headers = {
    "Content-Type": "application/json"
  };

  try {
    switch (event.routeKey) {
      case "DELETE /items/{id}":
        await dynamo
          .delete({
            TableName: "http-crud-tutorial-items",
            Key: {
              id: event.pathParameters.id
            }
          })
          .promise();
        body = `Deleted item ${event.pathParameters.id}`;
        break;
      case "GET /items/{id}":
        body = await dynamo
          .get({
            TableName: "http-crud-tutorial-items",
            Key: {
              id: event.pathParameters.id
            }
          })
          .promise();
        break;
      case "GET /items":
        body = await dynamo.scan({ TableName: "http-crud-tutorial-items" }).promise();
        break;
      case "PUT /items":
        let requestJSON = JSON.parse(event.body);
        await dynamo
          .put({
            TableName: "http-crud-tutorial-items",
            Item: {
              id: requestJSON.id,
              price: requestJSON.price,
              name: requestJSON.name
            }
          })
          .promise();
        body = `Put item ${requestJSON.id}`;
        break;
      default:
        throw new Error(`Unsupported route: "${event.routeKey}"`);
    }
  } catch (err) {
    statusCode = 400;
    body = err.message;
  } finally {
    body = JSON.stringify(body);
  }

  return {
    statusCode,
    body,
    headers
  };
};

```
{% endcode %}

## Create the HTTP API&#x20;

Create an **HTTP API** and give it the name  **http-crud-tutorial-api**.&#x20;

## Create routes

Routes are a way to send incoming API requests to backend resources. Routes consist of two parts: an HTTP method and a resource path, for example, `GET /items`. For this CRUD API, we create four routes:

* `GET /items/{id}` get item with a specific id
* `GET /items` get all the items&#x20;
* `PUT /items` create or update an item&#x20;
* `DELETE /items/{id}` delete an item

### Create a route&#x20;

The steps are the same for all routes. For example GET /items/{id}:

1. go to API gateway and the HTTP API we just created
2. Click Routes > create
3. For Method choose **GET**&#x20;
4. For path enter `/items/{id}`
5. Click create

Repeat the steps for these methods and paths:

* `GET /items`
* `DELETE /items/{id}`
* `PUT /items`

You should end up with the following:

![routes ](<../.gitbook/assets/image (340).png>)

## Create the integration

We're still in API gateway and the HTTP API.&#x20;

1. Choose Integrations
2. Choose Manage integrations > create
3. Skip "Attach this integration to a route"&#x20;
4. Integration type: Lambda
5. Lambda function is the **http-crud-tutorial-function**
6. Create.&#x20;

## Attach integration to routes

We're still in API gateway and the HTTP API.&#x20;

1. Choose Integrations
2. Choose Attach integrations to routes
3. Choose a route (e.g. GET /items)&#x20;
4. Under choose an existing integration, choose the Lambda **http-crud-tutorial-function**
5. Choose attach integration&#x20;

Repeat steps for all routes. This is what it looks like when GET /items route has an integration:

![route GET /items has an integration attached](<../.gitbook/assets/image (313).png>)

## Test the API&#x20;

Everything should be working now! You will find the invoke URL of your API from API Gateway. We can test it using cURL (any tool of your choice). If you want more information from cURL, use curl -v.&#x20;

The new items need to have&#x20;

* an id (which is sent as a String, therefore it needs quotes around it)&#x20;
* a price
* a name.&#x20;

### Create an item

```
curl -X "PUT" -H "Content-Type: application/json" -d "{"id": "1", "key1": "value1", "foo": "bar"}" https://invoke-url/items
```

### Update an item

```
curl -X "PUT" -H "Content-Type: application/json" -d "{"id": "1", "stuff": "here", "replace": "me"}" https://invoke-url/items
```

### Get all items

```
curl https://invoke-url/items
```

### Delete an item

```
curl -X "DELETE" https://abcdef123.execute-api.us-west-2.amazonaws.com/items/abcdef234
```

Go ahead and play around: create an item, update it, get it, delete it, get all items to make sure it's been deleted etc.&#x20;

## Clean up&#x20;

![](<../.gitbook/assets/image (58).png>)

Once you are done admiring your awesome creation, go ahead and delete

* The API&#x20;
* the Lambda function&#x20;
* the DynamoDB table.
