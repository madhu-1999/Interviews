#aws #cloud #database 
```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 4 # Include headings up to the specified level
include: 
exclude: 
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
# Overview
+ Relational Database Service (RDS)
+ [[#Managed Services|Managed]] relational database service that 
	+ makes it easier to set up, operate and scale relational databases in the cloud.
	+ handles routine database tasks such as backups, patching and hardware provisioning. 
+ Supports creation of different database engines, including [[Amazon Aurora]], MySQL, PostgreSQL, Microsoft SQL Server, MariaDB, and Oracle Database.
+ Can control access by using [[IAM]] to define users and permissions.
+ Can protect database by putting it in a [[Amazon VPC|VPC]].
>[!note]
>Below info is not valid for Amazon Aurora.
# DB instance
+ Isolated database environment in the cloud.
+ Can contain multiple user created databases.
+ Each instance has an identifier that must be unique for a customer in a region.
	+ This identifier is part of the DNS endpoint created for the DB instance.
## Access
+ Can be accessed using same tools as self hosted database instance.
+ SSH access is **NOT** allowed except with RDS Custom
+ CRUD through AWS CLI, RDS API or Management console^[[Interacting with AWS Services]]
## Instance classes
+ Instance class determines the memory and computation capacity of a DB instance.
	+  General purpose – db.m*
	- Memory optimized – db.z*, db.x*, db.r*
	- Compute optimized – db.c*
	- Burstable performance – db.t*
## Storage
+ The DB instance's primary data, logs and other files are stored in [[Amazon EBS|EBS]] volumes.
	+ Can use either [[Amazon EBS#**General Purpose SSD|General Purpose SSD]] or [[Amazon EBS#**IOPS provisoned SSD**|IOPS Provisioned SSD]] 
+ Automatically stripes data across EBS volumes.
	+ Incoming data us broken into smaller chunks called stripes.
	+ The stripes are written **simultaneously** to multiple underlying EBS volumes. 
		+ Note that same data is not written to all EBS volumes.
		+ Instead each stripe is written to a different volume simultaneously.
	+ This increases the total throughput and IOPS by a factor for _n_ , where _n_ is no of volumes.
# Read replicas
Refer [[Database Replication Strategies]] before continuing
+ Single leader setup + asynchronous updates (can have replication lag)
+ Ideal for read-heavy workloads.
+ Upto 15 read replicas per database instance (primary).
+ Each replica has a unique DNS endpoint, different from the primary.
	+ Application will have to update connection string and add the endpoints of read replicas.
	+ This way application knows to send reads to replicas and writes to primary.
+ Can be within same AZ, cross AZ or cross region.
	+ With cross AZ/region, read traffic can be served even if primary becomes unavailable.
+ [[#Auto Scaling]] not supported.
	+ Manually delete and create Read replicas.
+ If primary is deleted without deleting read replicas:
	+ If replica in same region, automatically promoted to standalone DB instance.
	+ If replica in different region, terminated
+ Can **manually** promote to primary in single AZ RDS setup, if primary fails.
	+ All read replicas will have to be recreated, since they still point to failed primary.
	+ Connection strings have to updated since read replicas have different DNS endpoint from primary.
![Read replica configuration](https://docs.aws.amazon.com/images/AmazonRDS/latest/UserGuide/images/read-replica.png)
## Creation
+ Take a [[Amazon EBS#EBS Snapshots|snapshot]] (EBS) of the primary instance
+ Create read replica from this snapshot.
	+ Snapshots can be copied across AZ and region.
+ By default, storage type is same as DB instance, but can be changed.
# Auto Scaling
+ Ideal for unpredictable workloads.
+ Scales storage on DB instance dynamically, when it detects you are running out of free storage.
+ Need to specify a **Maximum Storage Threshold**
	+ Auto scaling doesn't happen if it will cause storage to exceed this threshold.
+ Auto scales if:
	+ Free storage < 10% of total storage
	+ Low storage lasts atleast 5 minutes.
	+ 6 hours have passed since last scaling.
+ **Auto scaling cannot scale in**. 
	+ Can manually scale in by creating a new database instance and migrating to it.
+ Applies only to storage **not** for read replicas.
# Multi-AZ deployment
+ Provides high availability and disaster recovery.
## Multi-AZ instance deployment
+ Single leader, single standby
+ One standby instance that is copy of the primary DB instance in a different AZ.
	+ Updates are synchronous between standby and primary. (no replication lag)
	+ Passive in nature, **does not** serve reads.
	+ Takes over as primary if:
		+ primary DB instance fails
		+ AZ fails
		+ Storage failure
	+ Shares **same DNS endpoint** as primary instance
+ If standby takes over as primary, RDS automatically provisions a new standby in a different AZ.
+ The read replicas following the old primary are reconfigured by RDS to point to the new primary.
+ Both primary and the standby are backed by their own EBS volumes,
+ Automated snapshots taken of the standby (EBS snapshot)
![High availability scenario](https://docs.aws.amazon.com/images/AmazonRDS/latest/UserGuide/images/con-multi-AZ.png)
## Multi-AZ DB cluster deployment
+ Single leader, two standbys
+ Two standbys which are a replica of primary DB instance
	+ Semi-synchronous replication i.e. primary and atleast one standby must acknowledge receipt of data.
		+ Note that receipt of data is acknowledged, not commit. It is possible, data is not fully committed to standby. i.e replication lag can occur.
	+ Both serve reads.
	+ Failover is done either as:
		+ Both reader standbys, update unapplied transactions before one of them is promoted to primary. (MySQL)
		+ Standby with lowest replication lag, applies unapplied transactions and is then promoted to primary. (PostgreSQL)
	+ Storage is shared across multiple AZ's (EBS volumes). 
		+ Provides faster failover than traditional Multi-AZ because instances share the same storage
![Multi-AZ DB cluster](https://docs.aws.amazon.com/images/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster.png)
## Comparison
| Feature          | Multi-AZ DB Instance (Synchronous)                        | Multi-AZ DB Cluster (Semi-Synchronous)                                                        |
|------------------|-----------------------------------------------------------|-----------------------------------------------------------------------------------------------|
| Write latency    | Higher, due to waiting for full commit on the standby.    | Lower, often up to 2x faster, by using semi-synchronous replication and faster local storage. |
| Write throughput | Lower, as higher latency constrains the transaction rate. | Higher, allowing for better performance on write-intensive workloads.                         |
| Read scaling     | No, standby is passive.                                   | Yes, with two readable standby instances.                                                     |
| Failover time    | Typically longer (up to 60-120 seconds).                  | Faster (typically under 35 seconds).                                                          |
| Architecture     | Primary and one passive standby.                          | Primary writer and two readable standbys.                                                     |
| Storage type     | EBS volumes on both primary and standby instances.        | Shared storage across multiple AZ's (EBS volumes)                                             |

# Automated Backups
+ If enabled, **daily**, incremental (EBS volume) snapshots are taken automatically.
	+ In Single AZ, snapshot is of primary instance
	+ In Multi-AZ, snapshot is of standby.
+ Database transaction logs are streamed to S3, every 5 minutes.
+ A backup retention period is specified between 1-35 days, after which snapshot is deleted.
## Point in Time Recovery
+ Lets you restore database to a point a time down to the second (even if there is no snapshot for the specified time).
	+ To do this, the most recent daily snapshot before the specified recovery time is selected.
	+  All the relevant transaction logs are then applied to that snapshot to "roll forward" the database to the exact second requested.
	+ The process creates a brand new RDS instance with the restored data, leaving the original instance untouched.
	+ You can make this new database the new primary or export the required data from the new instance into the primary instance.
## Vs Manual snapshots

| Feature         | Automated backups (for PITR)                                                            | Manual snapshots                                                                                               |
| --------------- | --------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| **Trigger**     | Automatically created by RDS on a daily schedule.                                       | User-initiated on an ad-hoc basis.                                                                             |
| **Restoration** | Allows recovery to any second within the retention period.                              | Restores to the exact state captured at the time the snapshot was taken.                                       |
| **Retention**   | Automatically deleted after the retention period expires (1–35 days).                   | Stored indefinitely until you manually delete them.                                                            |
| **Purpose**     | Designed for operational recovery from common data loss or corruption events.           | Ideal for long-term archiving, creating test environments, or a known-good backup before major schema changes. |
| **Cost**        | Backup storage equivalent to your database size is free. Additional storage is charged. | Billed for storage from the moment they are created.                                                           |
# AWS Shared Responsibility Model
## Fully managed Services
![[Screenshot 2025-09-05 at 2.35.37 PM.png]]
## Managed Services
![[Screenshot 2025-09-05 at 2.36.51 PM.png]]
## Unmanaged Services
Ex: self-hosted db on a [[Amazon Elastic Compute Cloud (AWS EC2)#EC2 Instance|EC2]] instance.
![[Screenshot 2025-09-05 at 2.37.47 PM.png]]
# Security
## At-rest encryption
+ Database master and replicas can be encrypted using AWS KMS -- must be defined at launch time.
+ If master is not encrypted, read replicas cannot be encrypted.
+ To encrypt an unencrypted database, restore a DB snapshot as encrypted.
+ Cannot unencrypt an encrypted database but can export data from an encrypted one to an unencrypted one,
## Encryption in transit
+ TLS certificate specific to AWS region used to encrypt data.
+ Need to modify database connection settings to incorporate TLS certificate to do encryption client-side.
## IAM Authentication
+ Use [[IAM#Roles|IAM Roles]] to authenticate connection to database. 
## Network Security
+ Enforced through [[Amazon VPC#Security Groups|Security Groups]]
# [[Proxy server#Database Proxy|Database Proxy]]
+ [[#Fully managed Services|Fully managed service]]
+ Allows apps to pool and share DB connections.
	+ Improves efficiency by reducing stress on database resources (CPU, RAM, minimize open connections)
+ Serverless, autoscaling and highly available across multiple AZ's
+ Reduces Aurora and RDS failover time by 66%.
+ No code changes needed.
+ Enforce IAM authentication for DB.
+ **Never publicly accessible** until RDS which can be configured to be publicly accessible.
## Use Case: AWS Lambda Functions
+ AWS Lambda functions execute code in response to events and can scale rapidly, creating many instances that open connections to your RDS database. 
+ This can lead to a large number of open connections and potential timeouts, causing performance issues.
+ By using RDS Proxy, Lambda functions connect to the proxy instead of directly to the database. 
+ The proxy pools these connections, reducing the number of direct connections to the database instance and solving the problem of connection overload.