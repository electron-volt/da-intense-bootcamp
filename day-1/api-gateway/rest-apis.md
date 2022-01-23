# REST API's

## **REST API's under the hood**

We've built our very first serverless HTTP API. Now let's take a closer look at how API gateway actually works for REST API's (we will not discuss WebSocket API's). It looks something like this:

client -> method request -> integration request -> endpoint -> integration response -> method response -> client.&#x20;

Let's clarify these terms.&#x20;

### **Method request**

The public interface of an API method in API Gateway that defines the parameters and body that an app developer must send in requests to access the backend through the API.

### **Integration request**

The internal interface of a WebSocket API route or REST API method in API Gateway, in which you map the body of a route request or the parameters and body of a method request to the formats required by the backend.

### **Integration response**

The internal interface of a WebSocket API route or REST API method in API Gateway, in which you map the status codes, headers, and payload that are received from the backend to the response format that is returned to a client app.

### **Method response**

The public interface of a REST API that defines the status codes, headers, and body models that an app developer should expect in responses from the API.

Let's see this in action in the next lab.
