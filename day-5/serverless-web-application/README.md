# 🦄 Serverless web application

In this gigantic mother-of-all-labs we will build this:

![](<../../.gitbook/assets/image (268).png>)

In this tutorial, you'll create a simple serverless web application that enables users to request unicorn rides from the [Wild Rydes](http://www.wildrydes.com) fleet. 🦄

The application will present users with an HTML based user interface for indicating the location where they would like to be picked up and will interface on the backend with a RESTful web service to submit the request and dispatch a nearby unicorn. The application will also provide facilities for users to register with the service and log in before requesting rides.

We will build this lab in steps. Most of the tasks we are already familiar with, but there are two new technologies: Amplify and Cognito.&#x20;

## Amplify&#x20;

AWS Amplify hosts static web resources including HTML, CSS, JavaScript, and image files which are loaded in the user's browser.

## Cognito

Amazon Cognito provides user management and authentication functions to secure the backend API.

## Serverless backend

Amazon DynamoDB provides a persistence layer where data can be stored by the API's Lambda function.

## API Gateway

JavaScript executed in the browser sends and receives data from a public backend API built using Lambda and API Gateway.

