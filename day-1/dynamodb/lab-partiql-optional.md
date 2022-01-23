# LAB: PartiQL (optional)

If you are used to SQL, you know that you could get all the dogs of a given breed from the Dogs table with

`SELECT * from Dogs where breed="husky";`

DynamoDB obviously does not support SQL queries, but it does support PartiQL queries. PartiQL is an SQL-compatible query language for DynamoDB. It does not support table joins, but you can read data from one table at a time with SQL-like queries.

As a bonus lab, read through the following:

{% embed url="https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.html" %}

Then go back to the console. The DynamoDB service has a PartiQL editor where you can query your tables.&#x20;

Example queries:

`select ID FROM Dogs WHERE breed='husky'`

`select song, album FROM Music WHERE Artist='Nightwish'`
