#aws #cloud 
# Overview
+ [[Storage in AWS#Fully Managed Services|Fully managed]], highly available [[Storage in AWS#Object storage|object storage]].
+ **Infinitely scalable** i.e. can handle any amount of data and requests
	+ Auto scales on demand. No need to provision storage
	+ Flat structure of object makes it more efficient for high volume storage.
+ 99.99999999999% durability of data i.e. protection from data loss.
# Objects
+ Fundamental unit of data storage.
+ When you upload a file to S3, it becomes a object.
+ An object is stored durably across multiple AZ's in a region.
+ Each object includes
	+ data
	+ metadata (system or user)
	+ unique identifier or key. (file name)
	+ version id (if enabled)
	+ tags (key-value pairs up to 10)
	+ access control information
+ Can be of any type such as image, video or document, and up to 5 TB in size.
	+ If uploading more than 5 GB, must use **multi-part** upload.
+ Uniquely identified **within a bucket** by a composite key (key + version id).
+ Every object has a public URI through which it can be publicly accessed.
	+ If object is private (bucket policy, object ACL, block public access setting), then public URI does not work. A *presigned URI* is needed to access the object.
## Naming
+ Unicode characters with maximum size 1024 bytes.
+ Case-sensitive
+ Object key name includes any prefixes.
	+ `Development/Projects.xls` is full object key name of `Projects.xls` object located in `Development` prefix or folder.

>[!critical]
>+ Object **key** name is always the full path
>+ There is **no** hierarchy in S3, but using prefixes allows [[Interacting with AWS Services#AWS Management Console|console]] to infer hierarchy.
>	+ When you create a folder in a bucket, actually a **zero-byte** object is created with a key ending in a forward slash (ex: `myfolder/`).
>	+ Console is programmed to display such an object as an empty folder.
# General Purpose Buckets
+ Every object is stored in a bucket.
+ Every bucket must have a **globally unique name** across all regions, all accounts within a [[AWS Global Infrastructure#Partitions|partition]].
+ They are defined at the **region** level.
## Naming
+ Only lowercase letters, numbers, period (.) or hyphen (-).
+ 3-63 characters long
+ Not an IP
+ Must start and end with lowercase letter or number.
+ Must **not** start with prefix `xn--` ,`sthree-`  or `amzn-s3-demo-` 
+ Must **not** end with suffix `-s3alias` , `--ol-s3` , `.mrap` ,`--x-s3` or `--table-s3` 
## Block Public Access
+ By default, public access is blocked.
+ Can be set to bucket level or account level.
+ Has 4 settings (all applied by default):
	+ Block Public ACLs : Blocks new public ACLs from being applied to buckets or objects.
	+ Ignore Public ACLs: Ignores all public ACLs (already created) on buckets and objects.
	+ Block Public Bucket Policies: Prevents new public bucket policies from being applied.
	+ Restrict Public Buckets: Limit access to buckets to only bucket owner and authorized users/roles through policies.
## Object Ownership
+ Bucket-level setting to control ownership of objects uploaded to the bucket.
+ By default it is *Bucket owner enforced* , which means all ACLs on bucket are disabled.
	+ Bucket owner owns all objects uploaded to the bucket and has full access(r/w) to the bucket.
+ *Bucket owner preferred* means ACLs are enabled, but bucket owner owns and has full control over all new objects.
+ *Object writer* means the AWS account that uploads an object has full control over it. ACLs are enabled.
# [[IAM]] for S3
+ User based / Identity based
	+ IAM policies - which API calls should be allowed for a specific IAM user/role.
+ Resource based
	+ Bucket policies - bucket wide rules
	+ Object Access Control List - finer grained 
	+ Bucket Access Control List - less common
+ Recommended to use _bucket policies_ and _identity based policies_ and not ACLs.
## Bucket policy
+ Permissions apply to all objects in the bucket, **owned by bucket owner**.
+ Only bucket owner can attach this policy to a bucket.
+ Use cases:
	+ Grant **public access** to the bucket.
	+ Force objects to be encrypted at upload.
	+ Grant access to another account.
+ S3 Block Public Access settings override bucket policies.
# Storage Classes
## Frequent access
For latency sensitive (ms access time) frequently accessed data
### **S3 Standard**
+ Default storage class
+ Data stored redundantly across a minimum of 3 AZ's within a region.
+ ms access
### **S3 Express One Zone**
+ **Single-zone**, high performance storage class.
+ **Lowest latency**. 
	+ 10x faster and 50% cheaper than S3 standard.
+ Data stored redundantly on multiple devices in single AZ.
+ ms access
### **Reduced Redundancy Storage**
+ For non-critical, reproducible data that can be stored with less redundancy than S3 standard.
+ 0.01% chance of losing data annually.
## S3 Intelligent-Tiering
+ For optimizing access to data with **changing** or **unpredictable** data access patterns.
	+ Automatically moves data to most cost-effective tier, **without** performance impact or operational overhead.
### **Access Tiers**
+ ***Frequent access***
	+ Default storage class for objects uploaded to S3 Intelligent-Tiering.
+ ***Infrequent access***
	+ Objects are moved to this tier, if not accessed for 30 consecutive days.
+ ***Archive Instant access**
	+ Objects are moved to this tier if not accessed for 90 consecutive days.
	+ ms access, high throughput.
+ ***Archive access**
	+ Optional tier, needs to be activated.
		+ Bypasses *Archive Instant access* if activated 
	+ Use only for data that can be accessed **asynchronously**.
	+ Objects moved to this tier, if not accessed for 90 consecutive days.
	+ Lower cost than *Archive Instant access* 
	+ minute-to-hour retrieval time.
+ ***Deep Archive access***
	+ Optional tier, needs to be activated.
	+ Use only for data that can be accessed **asynchronously**.
	+ Objects moved to this tier, if not accessed for 180 consecutive days.
## Infrequent access
+ For storing backups
+ Older infrequently access data, but needs ms access.
+ Charged for a minimum 30 days storage duration.
	+ Charged for entire 30 days even if objects are deleted, overwritten or transitioned to a different storage class.
### **S3 Standard-IA**
+ ms access
+ Data stored redundantly across multiple AZ's .
+ Greater durability and availability than S3 One Zone-IA.
+ Use for primary or only copy of data that cannot be recreated.
### **S3 One Zone-IA**
+ ms access
+ Data stored in one AZ only.
+ Less expensive than S3 Standard-IA
+ Less durable and available than S3 Standard-IA.
	+ Data loss if AZ fails.
+ Use for data that can be recreated.
## Rarely accessed
+ Low cost, long term data storage and archiving.
### **S3 Glacier Instant Retrieval**
+ Use for data which might need to be retrieved in ms.
+ Real-time access.
### **S3 Glacier Flexible Retrieval**
+ Use for data which might need to be retrieved in minutes.
+ Archives data (no real time access)
### **S3 Glacier Deep Archive**
+ Archives data (no real time access)
+ 12 hour retrieval time
## S3 on [[AWS Global Infrastructure#AWS Outposts|Outposts]]
+ Only available for objects stored in buckets on Outposts.
+ Objects are encrypted using server side encryption with S3 managed encryption keys.
	+ Can also choose to use customer provided encryption keys.
## Comparison
| Storage class                                                     | Designed for                                                                                                  | Durability (designed for) | Availability (designed for)        | Availability Zones | Min storage duration | Min billable object size | Other considerations                                                                                                                                                                                                                                                                                                |
| ----------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------- | ------------------------- | ---------------------------------- | ------------------ | -------------------- | ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| S3 Standard (`STANDARD`)                                          | Frequently accessed data (more than once a month) with millisecond access                                     | 99.999999999%             | 99.99%                             | >= 3               | None                 | None                     | None                                                                                                                                                                                                                                                                                                                |
| S3 Standard-IA (`STANDARD_IA`)                                    | Long-lived, infrequently accessed data (once a month) with millisecond access                                 | 99.999999999%             | 99.9%                              | >= 3               | 30 days              | 128 KB                   | Per-GB retrieval fees apply.                                                                                                                                                                                                                                                                                        |
| S3 Intelligent-Tiering (`INTELLIGENT_TIERING`)                    | Data with unknown, changing, or unpredictable access patterns                                                 | 99.999999999%             | 99.9%                              | >= 3               | None                 | None                     | Monitoring and automation fees per object apply. No retrieval fees. Objects less than 128KB are not monitored and always stored in the Frequent Access tier. For more information, see [How S3 Intelligent-Tiering works](https://docs.aws.amazon.com/AmazonS3/latest/userguide/intelligent-tiering-overview.html). |
| S3 One Zone-IA (`ONEZONE_IA`)                                     | Recreatable, infrequently accessed data (once a month) with millisecond access                                | 99.999999999%             | 99.5%                              | 1                  | 30 days              | 128 KB                   | Per-GB retrieval fees apply. Not resilient to the loss of the Availability Zone.                                                                                                                                                                                                                                    |
| S3 Express One Zone (`EXPRESS_ONEZONE`)                           | Single-digit millisecond data access for latency-sensitive applications within a single AWS Availability Zone | 99.999999999%             | 99.95%                             | 1                  | None                 | None                     | S3 Express One Zone (`EXPRESS_ONEZONE`) objects are stored in a single AWS Availability Zone that you choose.                                                                                                                                                                                                       |
| S3 Glacier Instant Retrieval (`GLACIER_IR`)                       | Long-lived, archive data accessed once a quarter with millisecond access                                      | 99.999999999%             | 99.9%                              | >= 3               | 90 days              | 128 KB                   | Per-GB retrieval fees apply.                                                                                                                                                                                                                                                                                        |
| S3 Glacier Flexible Retrieval (`GLACIER`)                         | Long-lived archive data accessed once a year with retrieval times of minutes to hours                         | 99.999999999%             | 99.99% (after you restore objects) | >= 3               | 90 days              | NA*                      | Per-GB retrieval fees apply. You must first restore archived objects before you can access them. For information, see [Restoring an archived object](https://docs.aws.amazon.com/AmazonS3/latest/userguide/restoring-objects.html).                                                                                 |
| S3 Glacier Deep Archive (`DEEP_ARCHIVE`)                          | Long-lived archive data accessed less than once a year with retrieval times of hours                          | 99.999999999%             | 99.99% (after you restore objects) | >= 3               | 180 days             | NA**                     | Per-GB retrieval fees apply. You must first restore archived objects before you can access them. For information, see [Restoring an archived object](https://docs.aws.amazon.com/AmazonS3/latest/userguide/restoring-objects.html).                                                                                 |
| Reduced Redundancy Storage (`REDUCED_REDUNDANCY`) Not recommended | Noncritical, frequently accessed data with millisecond access                                                 | 99.99%                    | 99.99%                             | >= 3               | None                 | None                     | None                                                                                                                                                                                                                                                                                                                |

# S3 Lifecycle
+ Helps optimize cost of object storage throughout its lifecycle by
	+ Transitioning objects to lower cost storage classes.
	+ Deleting expired objects.
+ Best for data with **predictable data access patterns**.
+ Overrides [[#Bucket policy]].
+ Two types of actions
	+ ***Transition actions***
		+ Define when objects should transition to next storage class.
		+ Ex: Transition to [[#**S3 Standard-IA**|S3 Standard IA]] after 30 days and to [[#**S3 Glacier Flexible Retrieval**|S3 Glacier Flexible Retrieval]] after 90 days.
	+ ***Expiration actions***
		+ Define when objects should expire.
		+ S3 deletes expired objects.
		+ Ex: After compliance period ends.
+ Rules apply to both **existing** and **new** objects.
# Object Versioning
+ Enabled at bucket level
	+ Does not apply retroactively.
	+ Disabling/Suspending versioning does not delete previous versions.
+  Overwriting will create new object with same key but different version.
![Diagram depicting how S3 Versioning works when you PUT an object in a versioning-enabled bucket.](https://docs.aws.amazon.com/images/AmazonS3/latest/userguide/images/versioning_PUT_versionEnabled3.png)
+ Deleting an object means a delete marker is created.
![Illustration that shows a delete marker insertion.](https://docs.aws.amazon.com/images/AmazonS3/latest/userguide/images/versioning_DELETE_versioningEnabled.png)
+ Only bucket owner and authorized IAM us![Diagram that shows how DELETE versionId permanently deletes a specific object version.](https://docs.aws.amazon.com/images/AmazonS3/latest/userguide/images/versioning_DELETE_versioningEnabled2.png)er can **permanently delete** an object i.e. latest version is deleted.
+ Protects against accidental deletes/writes.
	+ Revert easily to previous versions
+ If not enabled, version id is **null**, else it is a unique id for the object.
+ `GET` operations fetch **latest version** of an object. (Default)
	+ Can fetch a specific version of an object by specifying version id
# Replication
+ Asynchronous, automatic replication of objects across buckets.
	+ Can replicate to single bucket or multiple buckets.
	+ Buckets can be in same AWS account or different AWS accounts.
	+ Buckets can be in the same region (SRR) or different regions (CRR).
+ Only **new** objects are replicated, once enabled i.e. replication does not happen retroactively.
	+ Existing objects can be replicated using S3 Batch replication.
## Delete operations
+ Delete markers can be replicated from source to target (optional setting)
+ Permanent deletes are not replicated, to protect data from malicious deletes.
## What is not replicated
+ No _chaining_ of replication. ^2f7159
	+ If bucket A is source and bucket B destination for a replication rule, and there is another replication rule where bucket B is source and bucket C is destination, then **objects replicated from bucket A to bucket B are not replicated to bucket C**.
+  If destination of replication changes, objects in source bucket that have already been replicated will not be replicated again. ^1269a6
+ Objects stored in archival storage such as S3 Glacier Flexible Archive cannot be replicated until they are restored into a different storage class.
+ Objects in the bucket that the bucket owner does not have permission to replicate.
## S3 Batch Replication
+ Replicate existing objects
+ Replicate objects that failed to replicate
+ Replicate objects in source bucket that have already been replicated [[#^1269a6]]
+ Replicate replicas of objects ([[#^2f7159|chaining of replication]])
## Requirements for replication
+ Source and destination buckets must have versioning enabled.
+ S3 must have appropriate IAM permissions to perform replications.
+ If bucket has objects bucket owner doesn't own, then object owner must provide bucket owner *READ* permission with object ACL.
+ Destination bucket owner must grant source bucket permission to replicate through a [[#Bucket policy|bucket policy]].
# Static Website Hosting
+ Can host static websites using a bucket i.e. bucket becomes server.
	+ Hosted on *website endpoint*, which is either:
		+ `http://<bucket-name>.s3-website-<Region>.amazonaws.com` (dash)
		+ `http://<bucket-name>.s3-website.<Region>.amazonaws.com` (dot)
+ ***Does not*** support HTTPS.
+ Bucket ***must*** be publicly readable.
# Use cases
+ Backup and storage
+ Disaster Recovery
+ Archive
+ Hybrid cloud storage
+ Application Hosting
+ Media Hosting
+ Data lakes and big data analytics
+ Software delivery
+ Static website