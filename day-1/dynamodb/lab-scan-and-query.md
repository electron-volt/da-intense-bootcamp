# LAB: Scan and query

## Part 1: run a scan

Let's navigate back to the DynamoDB service and our Dogs table. Navigate to the View items view. Under the table name it says "expand to query or scan items". Click on the black triangle to reveal this view:\


![](<../../.gitbook/assets/image (242).png>)

Let's add a filter. Depending on the items you added in your table, you can filter for specific breeds,  dogs that weigh over 30 kg, dogs that are over 2 years old etc.&#x20;

## Difference between a scan and a query

When we run a scan without any filters, the scan returns the entire table. We are able to also filter the results to only contain certain items. The catch is this: _the scan always goes through the entire table_, returns every item and the filter is applied afterwards.

Considering what we have learned about read capacity units, you can guess that for large tables a scan will consume a LOT of RCU's. With a query we are able to fetch an individual item from the table with its partition key.&#x20;

## Part 2: run a query&#x20;

If we scroll back up and select query instead of scan, the view changes to this:

![](<../../.gitbook/assets/image (241).png>)

Pick an ID and query the table. It will return only one result (if a dog with such an ID exists).&#x20;
