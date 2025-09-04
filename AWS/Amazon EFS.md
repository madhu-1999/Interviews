#aws #cloud
```table-of-contents
title: Index
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
+ Elastic File System for cloud and on-premise access. [[Network Attached Storage (NAS)]]
+ [[Storage in AWS#Fully Managed Services|Fully managed]], scalable file storage.
+ Operates on the Linux NFS protocol.
+ Automatic scaling without provisioning of storage capacity or managing it.
+ ==Pay only for storage used== as opposed to EBS where we pay for all provisioned capacity.
+ Can be accessed by multiple [[Amazon Elastic Compute Cloud (AWS EC2)|EC2]] instances simultaneously. 
	+ EFS exposes a endpoint (mount target), using NFS protocol that multiple instances can ==mount== concurrently
	+ Supports 1000+ concurrent NFS connections
![](https://cdn.prod.website-files.com/655bc1860a87f22da98dd83c/67ee9b6ab41627f664f12bd6_efs-ec2-how-it-works-OneZone.png)
+ ==Compatible only with Linux based [[Amazon Elastic Compute Cloud (AWS EC2)#Amazon Machine Image (AMI)|AMI]]== 
+ Encryption at rest using AWS KMS.
+ Can be used with EC2, Lambda, EKS, ECS and Fargate containers.
# Types of File systems
## Regional
+ Data stored across multiple AZ's in a region.
+ High durability and availability. 
+ Mount targets are created in each AZ assigned to subnet.
>[!note]+
>**Durability**: Ensure data is not lost due to failure. Redundancy, replication, RAID are techniques to improve data durability.
>**Integrity**: Ensure data is accurate, consistent and unaltered. Validation is done using checksums.
## One Zone
+ Data is stored in **one** AZ.
+ Lowers cost
+ Reduced durability and availability.
+ Suitable for non-critical and fault-tolerant workloads
# Storage classes
## Standard
+ SSD storage for frequently accessed data.
+ Lowest latency
+ Multi-AZ resilience
## Infrequent Access (IA)
+ Lower cost storage class for data that is infrequently accessed (historical data or backups).
+ Reading file from IA incurs a charge but you save on storage cost.
+ Multi-AZ resilience
## Archive
+ Lowest cost storage for rarely accessed data.
+ Retrieval is slower and there are fees to access and restore data from archive.
+ minimum 90 day retention.
## One Zone
+ Same as [[#Standard]]
+ Only in one AZ.
## One Zone IA
+ Same as [[#Infrequent Access (IA)]]
+ Only in one AZ
# EFS Data Lifecycle 
+ Optimize storage costs by automatically moving data between storage classes based on usage patterns.
+ Lifecycle policies are used to determine when and how files transition between different storage tiers.
	+ **Transition to IA** : 
		+ Move files from standard to IA if not accessed for a set period of time.
		+ By default, this period is 30 days.
	+ **Transition to Archive**:
		+ Move files from standard to archive if not accessed for a set period of time.
		+ By default, this period is 90 days.
	+ **Transition to standard**:
		+ Whether to move files out of IA/Archive and back to Standard if accessed in IA/Archive.
		+ By default, files are not moved back.
+ All writes to files in IA or Archive are first written to Standard, and can be transitioned to the applicable storage class after 24 hours.
# Performance Modes
+ Determines how latency vs throughput is optimized
## General Purpose
+ Lowest latency.
+ Default for all EFS file systems
+ [[#One Zone]] file systems **always** use this mode.
+ Ideal for latency sensitive workloads (web servers).
## Max I/O
+ Ideal for highly parallelized workloads that need ==higher throughput==.
+ Higher latency.
+ Can scale to improve total IOPS across file system.
+ Used for big data, analytics or log processing jobs.
+ ==Cannot be used with [[#Elastic Throughput]] or [[#One Zone]]== 
# Throughput Modes
>[!info]+
>+ Can switch existing file system's throughput mode and change throughput amount.
>+ After switching to Provisioned throughput mode or changing provisioned amount, 24-hour cooldown before:
>	+ Switching to Elastic or Burst mode
>	+ Decreasing provisioned amount
## Elastic Throughput
+ Default and recommended
+ Auto scale based on demand.
+ No need to provision throughput, pay for amount of data r/w each month.
+ Ideal for ==unpredictable== workloads.
## Provisioned Throughput
+ Provision specific throughput capacity (MiB/s).
+ Independent of amount of data stored.
+ Ideal for workloads which need ==consistently high throughput==, above what [[#Elastic Throughput]] provides.
+ Can burst above provisioned capacity with credits.
## Bursting Throughput
+ Throughput ==capacity== grows with amount of storage.
+ Base throughput is proportionate to [[#Standard|standard]] storage size.
+ Burst credits accrue when file system consumes throughput below its base rate.
+ Burst credits are deducted when throughput exceeds base rate.
+ Without burst credits, bursting still happens but at a lower rate.
# Access Control
+ [[Amazon VPC#Security Groups|Security Groups]] are used to control inbound and outbound traffic to EFS mount targets. 
	+ Allow outbound traffic from ==TCP port 2049== to let AWS services mount EFS.
+ Attach [[IAM#Policy|IAM policies]] to EFS to control which principals (users, roles) are allowed to perform actions.
+ Uses POSIX file system, so access at content level can be controlled by Unix user/group Ids and permission bits (rwx for owner, group and others).
+ *EFS Access points* for granular access:
	+ customizable entry point into EFS file system.
	+ When creating an access point, a root directory path is given. Only this directory and its child directories are ==visible== and accessible. 
		+ Root folder (/) is default.
	+ Can choose POSIX user/group which will perform all operations on the EFS. 
		+ It ignores the user/group at access time and makes it appear as if specified user/group is performing the action.
		+ All files/directories created will be owned by specified user/group.
	+ Access Points let you share a file system between multiple clients while isolating them at the file system level.
		+ Even if two access points have access to same directory, access to files and subdirectories is controlled by permission bits(rwx for owner, group and others.)
	+ Can integrate with IAM policies to require an application to use a specific role or access point to mount the file system.
## Mount target vs Access point
| Feature          | Mount Target                                                                                                              | Access Point                                                                                                                                             |
|------------------|---------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Purpose          | A network endpoint (Elastic Network Interface) for an EFS file system. It provides the core network connectivity.         | An application-specific entry point into an EFS file system for enforcing application-level permissions.                                                 |
| Scope            | Serves the entire EFS file system across a single subnet within an Availability Zone (AZ).                                | Restricts a client's view to a specific subdirectory and its contents.                                                                                   |
| Network Control  | Access is controlled primarily by the associated VPC security groups, which allow or deny traffic to the NFS port (2049). | Requires a mount target for network access but refines permissions using IAM policies and POSIX settings.                                                |
| Permission Model | Based on NFS-level users, groups, and permissions. Allows for mounting the entire file system.                            | Enforces a specified POSIX user and group ID (UID/GID) for all file system requests, overriding the NFS client's identity.                               |
| Authentication   | Can be controlled by security groups and file system policy but does not directly enforce a specific user identity.       | Can be combined with IAM policies to enforce specific IAM principals (users, roles) to use a specific access point.                                      |
| Configuration    | Configured per subnet within a VPC. It is required for network-level access to the EFS file system.                       | Configured on a per-application basis to enforce a specific path and POSIX identity. Multiple access points can be created for a single EFS file system. |
| Mount Command    | Involves mounting the file system DNS name or IP address.                                                                 | Requires the EFS mount helper and includes the access point ID in the mount command.                                                                     |
| Prerequisites    | An EFS file system must have at least one mount target to be accessible.                                                  | An EFS file system must have at least one mount target to use access points.                                                                             |

# Steps to create EFS file system
1. Choose [[#Types of File systems|file system type]]
2. Automatic backups can be enabled. Disabled by default.
3. Set lifecycle management policies.
4. Encryption at rest can be disabled. Enabled by default
5. Pick [[#Throughput Modes|throughput mode]]. Pick [[#Performance Modes|performance mode]] from additional settings
6. Pick VPC
7. Set security group, subnet and IP address for each AZ.
# Use Cases
1. CMS
2. Web serving
3. Data sharing
4. Database backups