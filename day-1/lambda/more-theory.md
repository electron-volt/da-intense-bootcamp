# More theory

## More useful Lambda theory&#x20;

We will not go over these topics in great detail now. Some of them will feature in other labs.&#x20;

### Memory and CPU&#x20;

By default your function has 128 MB of memory allocated to it. Memory can be set to 128 MB to 10 240 MB.&#x20;

You are not able to explicitly allocate CPU to your function. Instead AWS allocate CPU for your function proportional to how much memory is configured. More memory - more CPU.&#x20;

### Timeout

By default, your Lambda function will timeout in 3 seconds. The maximum timeout is 900 seconds or 15 minutes.&#x20;

### Environment variables

You can use environment variables to adjust your function's behavior without updating code. An environment variable is a pair of key-value strings. You create an environment variable for your function by defining a key and a value. Your function uses the name of the key to retrieve the value of environment variable.

You define environment variables on the unpublished version of your function. When you publish a version, the environment variables are locked for that version.

When would you use environment variables? For example, you can create two functions with the same code but different configurations: one connects to a test database, and the other to a production database. You use environment variables to tell the function the hostname and other connection details for the database.

🎯 Note: To increase database security, it is recommended that you use AWS Secrets Manager instead of environment variables to store database credentials. More about Secrets Manager later.

### Triggers&#x20;

So far we have invoked our functions manually for testing purposes. However Lambdas are more often automatically invoked by events, such as&#x20;

* an HTTP request
* a file upload
* changes to a database.

### Destination

A _destination_ is an AWS resource where Lambda can send events from an asynchronous invocation. You can configure a destination for events that fail processing (we will look at dead letter queues later). Some services also support a destination for events that are successfully processed.

### Concurrency&#x20;

_Concurrency_ is the number of requests that your function is serving at any given time. When your function is invoked, Lambda provisions an instance of it to process the event. When the function code finishes running, it can handle another request. If the function is invoked again while a request is still being processed, another instance is provisioned, increasing the function's concurrency.

There are two types of concurrency controls available:

* Reserved concurrency – Reserved concurrency guarantees the maximum number of concurrent instances for the function. When a function has reserved concurrency, no other function can use that concurrency. There is no charge for configuring reserved concurrency for a function.
* Provisioned concurrency – Provisioned concurrency initializes a requested number of execution environments so that they are prepared to respond immediately to your function's invocations. Note that configuring provisioned concurrency incurs charges to your AWS account.

### Layers

Lambda layers provide a convenient way to package libraries and other dependencies that you can use with your Lambda functions. Using layers reduces the size of uploaded deployment archives and makes it faster to deploy your code.&#x20;

A layer is a .zip file archive that can contain additional code or data. A layer can contain libraries, a custom runtime, data, or configuration files.&#x20;

You can use layers only with Lambda functions deployed as a .zip file archive.&#x20;

## Debugging

We will encounter X-Ray in a lab later this week.&#x20;
