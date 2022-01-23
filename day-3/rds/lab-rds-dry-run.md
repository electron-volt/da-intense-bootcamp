# LAB: RDS dry run

We will _almost_ create a database. We will do everything necessary to create a MySQL database, except press the final create button. We are interested in RDS and not the inner workings of a MySQL database, so we don't need to create a db just to delete it seconds later.&#x20;

If we wanted to connect to the database, we would need a client like SQL workbench. We would not be able to connect to the RDS host directly.&#x20;

If we needed a database that we have complete control over and complete access to, then we could of course launch an EC2 instance and install a database on it. Then we would lose all the benefits of the managed aspect of RDS, like the automated backups and so on.&#x20;

## Creating a database

Navigate to RDS.

Click **Create database**.

Under engine options, let's choose MySQL.

For **Templates**, let's pick Dev/test.

**DB instance identifier**: the name of the database

**Credentials**: you can set a master password

**Instance class**: if we were actually creating a database, the burstable t class would be the cheapest (it includes the free tier eligible instance types)

**Storage**: notice the option for storage autoscaling. This allows the database to grow past the initial storage capacity, if needed. Useful but potentially expensive.&#x20;

**Multi-AZ**: more about that later.&#x20;

**Connectivity**: you can choose a VPC to put the database in. You can also enable public access if you want to connect to your database over the public internet - not exactly ideal..

**Authentication options**: you can choose stronger options than just username + password.&#x20;

Then you get an estimate of how much the database would cost per month. Try changing some parameters: choose the Prod template, increase the storage, change the instance type etc and see how expensive you can make it.&#x20;

### Cancel

When you are done exploring, go ahead and "cancel".&#x20;
