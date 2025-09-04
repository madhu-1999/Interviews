#aws #cloud 
```table-of-contents
title: 
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
include: 
exclude: 
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
# Overview
+ Elastic Block Store (EBS)
+ Data persists even after instance is terminated or stopped.
+ [[Storage in AWS#Block storage|Block-level storage]] , offering consistent and low-latency performance for workloads like databases.
+ EBS volumes can be backed up, resized and reattached to different [[Amazon Elastic Compute Cloud (AWS EC2)|EC2]] instances.
+ ==Bound to a specific AZ==.
+ Can customize size (in GBs) and IOPS.
	+ Billed for entire provisioned capacity, even if unused.
	+ Can scale over time.
+ Delete on Termination attribute: Controls EBS behaviour when EC2 instance is terminated.
	+ By default, root EBS volume is deleted.
	+ By default, any other volume is not.
# Steps to create a Volume
1. Select EBS Volume Type
2. Enter size in GB
3. Default IOPS depends on volume type.
	1. Some volume types (ex: gp3) allow provisioning of extra IOPS within a limit.
	2. For others IOPS scales with volume size.
4. Select AZ. ==should be in same AZ as EC2 instance==.
5. (optional) Select snapshot id to create volume from.
6. (optional) Encrypt volume.
After creating,  go to EBS->Actions->Attach Volume to attach it to a EC2 instance.
>[!warning]+
>To use the EBS volume, it needs to be first formatted and then mounted onto the EC2 instance.

# EBS Snapshots 
+ Make a backup of your EBS volume at a point in time.
+ Not necessary, ***but*** recommended to detach volume before taking snapshot.
+ Can copy snapshots across AZ or region (redundancy or data migration).
+ Amazon Data Lifecycle Manager ([[Storage in AWS#Shared Responsibility for Storage services#Fully Managed Services|fully managed service]])  can schedule snapshots during off-peak hours to minimize performance impact and automatically delete outdated backups to control storage costs.
## Standard tier
+ Takes ==incremental backups== i.e. only copies what has changed since previous snapshot was taken.
	+ Each incremental snapshot contains reference to previous snapshot for portions of data that are unchanged.
	+ When a snapshot is deleted, only the data that is unique to the snapshot is removed. Data referenced by other snapshots is preserved.
	+ Ex: 
		Snapshot A has 10 blocks of data. 
		Data in blocks 1-4 changes
		Snapshot B is taken.
		Delete snapshot A.
		Then block 1-4 data in A is deleted since B has latest data. Data in Blocks 5-10 is preserved and reassigned to B, since it was unchanged.
+ Makes subsequent snapshots faster and more storage efficient => cost reduction
+ Can be used to create new volumes from a snapshot. The new volume is an ==exact copy== of the original volume at the time the snapshot was taken.
+ ==Snapshots are redundantly stored in multiple AZ's using [[Amazon S3]].==
## Archive Tier
+ Use for low-cost, long-term storage of rarely accessed snapshots that do not need frequent or fast retrieval.
+ When a snapshot is archived, it is converted to a full snapshot from an incremental one.
+ On accessing an archived snapshot, it is restored to the standard tier. Can take 24- 72 hours to restore.
+ Upto 75% discount on storage costs for snapshots stored for 90+ days.
+ ==Can't use a archived snapshot to create a EBS volume==
+ Can't enable [[#Fast snapshot restore|fast snapshot restore]] for an archived snapshot. Must be restored before enabling.
+ ==Can't cancel the snapshot archive/restore process after it has started==.
Reading: [Guidelines and best practices for archiving Amazon EBS snapshots](https://docs.aws.amazon.com/ebs/latest/userguide/archiving-guidelines.html)
## Recycle Bin
>[!note]+
>+ This feature can only be accessed by granting [[IAM]] access.
>+ Can also recover AMI through it.
+ Setup retention rules to recover **deleted** EBS snapshots after a accidental deletion.
+ Rules can apply to all snapshots, or to snapshots that include a specified set of tag/value pairs. 
+ Each rule specifies a retention period (between one day and one year), after which the snapshot is permanently deleted.
+ Rules can be changed any time but new rules don't apply retroactively. i.e. Changing a rule after an item has been deleted will not alter the retention period for the item.
+ [[#Fast snapshot restore|Fast snapshot restore]] is disabled when snapshot is deleted, and thus also when it is restored.
+ Resources in recycle bin are charged usual price
## Use Cases
+ Disaster recovery
+ Data migration
+ Volume resizing
+ Backup of production workloads
## Shared Responsibility
### **User**
+ Schedule and manage EBS snapshots regularly.
+ Monitor and delete unnecessary snapshots to avoid charges.
+ Encrypting snapshots, verifying their integrity.
# EBS volume lifecycle
![The lifecycle of an EBS volume.](https://docs.aws.amazon.com/images/ebs/latest/userguide/images/volume-lifecycle.png)
## Fast snapshot restore
+ When a volume is created from a snapshot, the block data is **lazy loaded** i.e. it is pulled from S3 only when it is accessed.
+ This can cause latency when block is accessed for the first time. 
+ Over time as more blocks are read, performance improves until it finally reaches provisioned capacity.
+ Fast Snapshot restore eliminates this latency by moving snapshot data from s3 to EBS volume in the background, fully initializing it before it is accessed for the first time.
+ Enabled explicitly per snapshot. Max limit 16 TiB.
### Advantages
+ Faster restore
+ Ensures new volumes perform consistently since first use.
+ [[Amazon Elastic Compute Cloud (AWS EC2)#**Custom AMI**|If a new EC2 instance is created from an existing EC2 instance]], the AMI automatically takes snapshot of root EBS volume. If FSR is enabled, EC2 instance boots up faster, since lazy-loading does not happen.
# EBS Volume Types
Reference: [EBS Volume Types](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-volume-types.html)
>[!note]+
>IOPS => no of R/W ops per second
>Throughput => volume of data transferred over time (Mbps)
## Solid State Drive (SSD) 
+ Optimized for ==transactional== workloads with ==frequent R/W== operations, ==small I/O== 
### **General Purpose SSD
+ gp2, gp3 
+ Balances price and performance for variety of workloads.
+ System boot volume (default root volume), dev and test env
###  **IOPS provisoned SSD**
+ io1, io2 Block Express
+ Highest performance SSD for mission critical ==low latency== or ==high throughput== workloads.
+ Great for database workloads
+ Allows [[#EBS Multi Attach]]
## Hard disk drive (HDD)
+ Optimized for large ==streaming== workloads with ==high throughput== needs
+ Cannot be boot volume.
### **Throughput optimized HDD**
+ st1
+ ==Low cost== HDD for ==frequently accessed, high throughput== workloads
+ Bid data, data warehouse, log processing
### **Cold HDD**
+ sc1
+ ==Lowest cost== HDD for ==infrequently accessed== workloads
# EBS Multi Attach
+ Only for [[#**IOPS provisoned SSD**|io1/io2]] .
+ Can attach same volume to multiple instances (upto 16 Nitro-based) in the ==same AZ==.
+ Must use a ==cluster aware== file system (GFS2, OCFS2) designed to handle concurrent R/W.
+ [[AWS Cloudwatch|Cloudwatch]] metrics aggregated over all instances, so cannot monitor performance for individual insatnce.
+ All instances have full R/W access.
+ Eliminates SPOF. If one instance fails, another can take over without need to detach and reattach the volume, reducing recovery time.
+ Use case:
	+ Multiple db nodes can access shared volume for concurrent writes, reads.
	+ Multiple instances can access and process a shared content repository, improving efficiency and throughput for web servers or content pipelines.
>[!note]+ 
>Nitro is hypervisor for some EC2 instance types.

