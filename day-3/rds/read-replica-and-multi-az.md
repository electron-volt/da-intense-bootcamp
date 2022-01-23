# Read replica & Multi-AZ

If we had a very important database in AWS running on RDS, we would not want to have that one instance be

* a bottleneck for performance
* a single point of failure.

To solve those problems, we can use Read replicas and/or Multi-AZ deployments.&#x20;

## Multi-AZ - high availability

In a Multi-AZ DB instance deployment, Amazon RDS automatically provisions and maintains a synchronous standby replica in a different Availability Zone. The primary DB instance is synchronously replicated across Availability Zones to a standby replica to provide data redundancy and minimize latency spikes during system backups. Running a DB instance with high availability can enhance availability during planned system maintenance. It can also help protect your databases against DB instance failure and Availability Zone disruption.

![all reads and writes go to the primary](<../../.gitbook/assets/image (349).png>)

If the primary instance fails, or is rebooted then the standby replica will be promoted to the primary.&#x20;

## Read replica - performance&#x20;

Notice how in the Multi-AZ deployment all reads and writes still go to the primary? What if there are many reads and we would like to offload some of them to the replica?The solution to this is a read replica (RR).&#x20;

Amazon RDS uses the MariaDB, Microsoft SQL Server, MySQL, Oracle, and PostgreSQL DB engines' built-in replication functionality to create a special type of DB instance called a read replica from a source DB instance. The source DB instance becomes the primary DB instance. Updates made to the primary DB instance are asynchronously copied to the read replica. You can reduce the load on your primary DB instance by routing read queries from your applications to the read replica. Using read replicas, you can elastically scale out beyond the capacity constraints of a single DB instance for read-heavy database workloads.

![Read replica](<../../.gitbook/assets/image (271).png>)
