# LAB: GSI and LSI

## Adding items via the CLI&#x20;

For this lab to be a little more interesting, you may want to add a few more items to the Dogs table so that there is more than just 1 dog of a certain breed. You can do this via the console like we did before or you might want to do it via the CLI. To add a dog with ID 15 and breed husky, run the following:

`aws dynamodb put-item --table-name Dogs --item '{"ID": {"N": "15"}, "breed": {"S": "husky"}}'`

This is my example table that has two rottweilers and three huskies:

![example Dogs table](<../../.gitbook/assets/image (189).png>)

## Part 1: GSI&#x20;

Let's create a global secondary index for our Dogs table.

* [ ] Navigate to DynamoDB and the Dogs table
* [ ] Go to Indexes and press Create index
* [ ] Use breed as partition key and ID as sort key&#x20;
* [ ] The system will suggest breed-ID-index as the name, that's fine.&#x20;
* [ ] Leave all other settings as they are and hit "create index".&#x20;

The index creation takes a while. Just wait until the Status turns to Active.

### Query for a certain breed

Once the index has been successfully created, let's go back to View items. We want to run a query,  not on the Dogs table but the new breed-ID-index. I will query my table for all dogs whose breed is Husky.&#x20;

![](<../../.gitbook/assets/image (129).png>)

Running the same query for rottweiler:

![it works as advertised](<../../.gitbook/assets/image (300).png>)

## Part 2: LSI

We just created a GSI by pressing a button that said "create index". It didn't ask us to specify if we want a local or global index. Has AWS learned to read minds? Possibly. But also for a table that already exists you can **only** create a GSI. The only time when you are able to create an LSI is when the table itself is being created - so we must create a new table.&#x20;

### Create a new table

* [ ] Go to DynamoDB and Create a new table&#x20;
* [ ] Name of the table is Music
* [ ] Partition key is artist (String)
* [ ] Sort key is song (String)
* [ ] Under settings select Customize settings
* [ ] Leave table class and all read/write settings as they are
* [ ] Under secondary indexes, click "Create local index"
  * [ ] Sort key is album (String)
  * [ ] Index name is album-index
  * [ ] Attribute projections: all
  * [ ] Create index
* [ ] Create table

Once the table has been created, add some items there. Add three attributes for each item: artist, song and album. These can be actual songs or the hit song Foo off the album Bar by artist Asdf. Great song.&#x20;

### Query the index

Here is an example of what the Music table might look like:

![example Music table](<../../.gitbook/assets/image (175).png>)

Let's try to find all the songs from Nightwish's album Once:

![](<../../.gitbook/assets/image (339).png>)

There you have it. In the actual base table Music we have a composite key of artist and song. So Artist-song is a key that uniquely defines one item. On the LSI however the composite key is Artist-album.&#x20;

🧩 Bonus: create a global secondary index for the Music table.&#x20;
