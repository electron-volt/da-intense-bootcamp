# LAB: Streams

## DynamoDB Streams and Lambda

In this lab we will enable a DynamoDB Stream on a table, associate the stream with a Lambda function and then make changes to the table.&#x20;

### Create the Lambda function

#### Create the execution role

* [ ] Go to IAM > Roles
* [ ] Choose create role
* [ ] Create a role with the following properties:
  * trusted entity: Lambda
  * Permissions: **AWSLambdaDynamoDBExecutionRole**
  * role name**: lambda-dynamodb-role**

Make note of the ARN of this role, it will be of the form

arn:aws:iam::acct-id:role/lambda-dynamodb-role.

#### Create the function

We will be using the CLI to create the function. You can of course use the console as well.&#x20;

* [ ] Make a file called index.js and paste this code into it

{% code title="index.js" %}
```javascript
console.log('Loading function');
exports.handler = function(event, context, callback) {   
    console.log(JSON.stringify(event, null, 2)); 
    event.Records.forEach(function(record) { 
        console.log(record.eventID); 
        console.log(record.eventName); 
        console.log('DynamoDB Record: %j', record.dynamodb); 
    }); 
    callback(null, "This is a message from Lambda"); 
};
```
{% endcode %}

Create a deployment package with&#x20;

`zip function.zip index.js`

* [ ] Then create the function with (replace the correct ARN for your execution role)

`aws lambda create-function --function-name ProcessDynamoDBRecords`\
`--zip-file fileb://function.zip --handler index.handler --runtime nodejs12.x`\
`--role arn:aws:iam::<your-account-id>:role/lambda-dynamodb-role`

#### Test the function

Create a file called input.txt and paste the following into it

{% code title="input.txt" %}
```json
{ "Records":[ { "eventID":"1", "eventName":"INSERT", "eventVersion":"1.0", "eventSource":"aws:dynamodb", "awsRegion":"us-east-1", "dynamodb":{ "Keys":{ "Id":{ "N":"101" } }, "NewImage":{ "Message":{ "S":"New item!" }, "Id":{ "N":"101" } }, "SequenceNumber":"111", "SizeBytes":26, "StreamViewType":"NEW_AND_OLD_IMAGES" }, "eventSourceARN":"stream-ARN" }, { "eventID":"2", "eventName":"MODIFY", "eventVersion":"1.0", "eventSource":"aws:dynamodb", "awsRegion":"us-east-1", "dynamodb":{ "Keys":{ "Id":{ "N":"101" } }, "NewImage":{ "Message":{ "S":"This item has changed" }, "Id":{ "N":"101" } }, "OldImage":{ "Message":{ "S":"New item!" }, "Id":{ "N":"101" } }, "SequenceNumber":"222", "SizeBytes":59, "StreamViewType":"NEW_AND_OLD_IMAGES" }, "eventSourceARN":"stream-ARN" }, { "eventID":"3", "eventName":"REMOVE", "eventVersion":"1.0", "eventSource":"aws:dynamodb", "awsRegion":"us-east-1", "dynamodb":{ "Keys":{ "Id":{ "N":"101" } }, "OldImage":{ "Message":{ "S":"This item has changed" }, "Id":{ "N":"101" } }, "SequenceNumber":"333", "SizeBytes":38, "StreamViewType":"NEW_AND_OLD_IMAGES" }, "eventSourceARN":"stream-ARN" } ] }
```
{% endcode %}

Then run this command

```
aws lambda invoke --function-name ProcessDynamoDBRecords --payload fileb://input.txt outputfile.txt
```

You can verify that the outputfile.txt has the message "This is a message from Lambda".

### Enable the stream

* [ ] Navigate to DynamoDB and the Dogs table
* [ ] Go to exports and streams
* [ ] Under DynamoDB Streams details, click Enable
* [ ] "Key attributes only" should be selected already. Click Enable.&#x20;

The stream is now created. Take note of the ARN of the stream. &#x20;

arn:aws:dynamodb:aws-region:acct-id:table/tablename/stream/yyyy-mm-ddThh:mm:ss

### Create the Event Source Mapping

Next we need to create an event source mapping. This way Lambda knows to poll for new events in the stream. Run the following command, using the ARN of the stream we just created:

`aws lambda create-event-source-mapping --function-name ProcessDynamoDBRecords --starting-position LATEST --event-source-arn <stream arn>`

### Make changes to the table

Next we want to test if this mapping works. Make changes to the DynamoDB table: edit, create and delete some items. You can verify from CloudWatch logs that the Lambda function gets triggered.

### End result

There you have it: a Lambda function that gets triggered when an item in a DynamoDB table gets created, modified or deleted. Our example is quite modest, but it does illustrate the main principle.&#x20;
