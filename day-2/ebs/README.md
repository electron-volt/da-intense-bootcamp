---
description: A little theory.
---

# EBS

Amazon Elastic Block Store (Amazon EBS) provides block level storage volumes for use with EC2 instances. EBS volumes behave like raw, unformatted block devices. You can mount these volumes as devices on your instances.

## Features of EBS

### Flexibility

EBS volumes support live configuration changes while in production. You can modify volume type, volume size, and IOPS capacity without service interruptions.&#x20;

### Encryption

For simplified data encryption, you can create encrypted EBS volumes with the Amazon EBS encryption feature. All EBS volume types support encryption.

### Persistence

EBS volumes that are attached to a running instance can automatically detach from the instance with their data intact when the instance is terminated if you uncheck the **Delete on Termination** check box when you configure EBS volumes for your instance on the EC2 console. The volume can then be reattached to a new instance, enabling quick recovery. If the check box for **Delete on Termination** is checked, the volume(s) will delete upon termination of the EC2 instance.

### Snapshots

Amazon EBS provides the ability to create snapshots (backups) of any EBS volume. Snapshots are stored in S3 (more about that later). Snapshots are incremental backups, meaning that only the blocks on the volume that have changed after your most recent snapshot are saved. If you have a volume with 100 GiB of data, but only 5 GiB of data have changed since your last snapshot, only the 5 GiB of modified data is written to Amazon S3. Even though snapshots are saved incrementally, the snapshot deletion process is designed so that you need to retain only the most recent snapshot.

## EBS volume types

EBS offers both SSD and HDD options. All the details are here: [https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html)

### SSD&#x20;

#### General purpose (gp2 and gp3)&#x20;

Good for dev and test environments or low-latency interactive applications.&#x20;

#### Provisioned IOPS (io1, io2 and io2 Block Express)&#x20;

For workloads that require sustained IOPS performance.&#x20;

### HDD&#x20;

#### Throughput optimized (st1)

Big data, data warehouses, log processing.

#### Cold HDD (sc1)

Infrequently accessed data. The cheapest alternative.&#x20;

## Instance store

Also called ephemeral store. This storage is located on disks that are physically attached to the host computer. Instance store is ideal for temporary storage of information that changes frequently, such as buffers, caches, scratch data, and other temporary content, or for data that is replicated across a fleet of instances, such as a load-balanced pool of web servers.

The data in an instance store persists only during the lifetime of its associated instance. If an instance reboots (intentionally or unintentionally), data in the instance store persists. However, data in the instance store is lost under any of the following circumstances:

* The underlying disk drive fails
* The instance stops
* The instance hibernates
* The instance terminates

Therefore, do not rely on instance store for valuable, long-term data.

