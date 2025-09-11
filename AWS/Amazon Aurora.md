#aws #cloud 
# Prerequisite
[[Amazon RDS]]
# Overview
+ Proprietary technology that is compatible with MySQL and PostgreSQL i.e. the Aurora database accepts connections using MySQL and PostgreSQL drivers.
+ Cloud optimized, 5x faster than MySQL and 3x faster than PostgreSQL.
+ Supports [[Amazon RDS#Automated Backups|automated backups and point in time recovery]]
+ Fast failover recovery (<60s) and database cloning.
+ Access through RDS.
# Storage
+ High-throughput, fault-tolerant, distributed storage similar to [[Amazon RDS#Multi-AZ DB cluster deployment|Multi-AZ DB cluster]]. i.e. it is shared storage amongst all DB instances.
	+ Makes data independent of DB instances in the cluster. Viewed by the instances as a single, logical volume, although it is made up of multiple storage nodes across AZ's.
	+ Low replication lag since all read replicas share same storage as primary.
	+ Quick deployment of read replicas/new DB instances since there is no need to copy storage of primary instance.
	+ Data is deleted only when the entire cluster is deleted.
+ Autoscales from 10GB to 128TB (only charged for used storage)
+ Automated backups to S3 for point in time recovery.
+ Self healing and fault tolerant
	+ 6 copies of same data stored across 3 AZ's, synchronous replication
	+ Automatically detects and repairs failures in the disk volumes that make up the cluster volume, by using data in other volumes.
## Storage configurations
1. I/O optimized: For I/O intensive applications
2. Standard: For moderate I/O usage
# High availability
+ Automates and standardizes database clustering and replication.
+ A DB cluster in Aurora consists of one or more DB instances and a cluster volume (shared storage) that manages the data for those DB instances.
	+ One primary (writer) instance and upto 15 read replicas across AZ's, updated asynchronously
	+ **Read replicas autoscale** and support **cross-region replication**
	+ All reads are directed to a _reader endpoint_ that load balances connections to the read replicas.
	+ In case, primary fails, a read replica takes over based on assigned priority. (0$\rightarrow$ highest, 15$\rightarrow$lowest)
		+ New primary is reconfigured to have same DNS endpoint as old one. (called _cluster endpoint_)
	+ For **writes**, it requires **four out of six copies** to be available, so if one AZ goes down, writes can continue uninterrupted. 
	+ For **reads**, it requires **three out of six copies**, ensuring high availability for read operations.
##
![Amazon Aurora DB cluster architecture diagram showing storage layer, database instances, and client connections.](https://docs.aws.amazon.com/images/AmazonRDS/latest/AuroraUserGuide/images/aurora_architecture.png)
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
