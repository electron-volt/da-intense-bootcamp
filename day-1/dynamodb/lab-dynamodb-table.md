# LAB: DynamoDB table

## Creating our first DynamoDB table

You should be logged in to the AWS console as the admin user. Navigate to DynamoDB by searching for it or locating it under All services > Database.&#x20;

We will create a table called Dogs. An item in the table represents one dog. Dogs have attributes like ID, name, age and breed. Since we have a schemaless database, some items can have more attributes than others.&#x20;

We will use ID as the partition key. Every dog has a unique ID that it is identified by.&#x20;

You have two options: either work in the console or with the CLI.&#x20;

🧩 Bonus option: If you want to try both, you can create a Dogs table in the console and a Cats table via the CLI.&#x20;

### Create the table

#### In the console

Once you're in the DynamoDB service, you should see a bright orange button that says "Create table".&#x20;

* [ ] Give your table a name "Dogs"
* [ ] Use ID as the partition key. It is a number.&#x20;
* [ ] Do not specify a sort key
* [ ] Leave the other settings as they are
* [ ] Click "create table"

AWS is now creating the table. Wait for the status to turn to "Active".&#x20;

![Table Dogs is now ready](<../../.gitbook/assets/image (161).png>)

#### Via the CLI

The table can also be created via the CLI like so

`aws dynamodb create-table --table-name Dogs --attribute-definitions AttributeName=ID,AttributeType=N --key-schema AttributeName=ID,KeyType=HASH --provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1`

### Create an item&#x20;

#### In the console

Let's click on the table Dogs to view more information. You will see an overview of the table's information.&#x20;

* [ ] Click on Actions and Create item.&#x20;

![](<../../.gitbook/assets/image (82).png>)

* [ ] For the ID attribute, put "1"
* [ ] Then click "Add new attribute" and choose String
* [ ] For this new attribute, give it the attribute name of "breed" and value of rottweiler
* [ ] Then click "Add new attribute" and choose Number
* [ ] For this new attribute, give it the attribute name of "age" and value of 4
* [ ] Click "Add new attribute" and choose String
* [ ] For this new attribute, give it the attribute name of "name" and value of Fluffy (or something)&#x20;

You should have something like this:

![item in the Dogs table](<../../.gitbook/assets/image (173).png>)

* [ ] Click "Create item".&#x20;

Go ahead and add some more items to your table. All the dogs must have an ID (number) but you can select the other attributes: breed, name, age, weight, sex, favourite toy, owner, favourite food, whatever.

#### Via the CLI&#x20;

An example of how you can create items via the CLI:

`aws dynamodb put-item --table-name Dogs --item '{"ID": {"N": "6"}, "breed": {"S": "husky"}, "eyes": {"S": "blue"}}'`

Note: even though ID is a number, it has to be sent as a String. From the CLI reference: Numbers are sent across the network to DynamoDB as strings, to maximize compatibility across languages and libraries. However, DynamoDB treats them as number type attributes for mathematical operations.

### View the items

Once you have populated the table with some more items, click on "View items" next to Actions.&#x20;

Your table might look like this:

![Dogs table](<../../.gitbook/assets/image (67).png>)

### Edit an item

Now let's try editing an item.&#x20;

* [ ] Click the checkbox of any item
* [ ] Under actions, select edit
* [ ] Make whatever changes you like
* [ ] Save changes

If the edit was successful, you'll see this banner:

![Wonder what that means](<../../.gitbook/assets/image (70).png>)

### End result

You now have a table called Dogs that stores information about dogs. Note: tables are regional. If you switch over to a different AWS region, you will be able to create a table called Dogs there as well. From AWS's perspective, these tables have nothing in common despite having the same name and belonging to the same account. The ARN of your table will be&#x20;

**arn:aws:dynamodb:region:acct-id:table/Dogs**

so it will always be region-specific.&#x20;

![](<../../.gitbook/assets/image (368).png>)
