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
	+ If object is private (bucket policy, object ACL, block public access setting), then public URI does not work. A presigned url is needed to access the object.
+ User defined metadata key names must start with `x-amz-meta-`
+ Object tags are useful for fine-grained permissions (only access specific objects with specific tags)
+ **Cannot search object metadata or tags**
	+ Must use an external DB like DynamoDB as a search index.
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
## Presigned url
+ Secure, time limited link that grants temporary access to a specific S3 object.
	+ Allows a user without necessary AWS credentials or permissions to upload/download/view an object for a limited time.
+ Presigned url embeds the credentials of the AWS user who generated it into the url along with expiration time (after which url becomes invalid).
	+  Allows only those actions which the embedded credentials have permission to perform.
	+ Allows access to only those objects which the embedded credentials have permission to access.
+ Can be generated using [[Interacting with AWS Services|Console, SDK or CLI]].
+ Expiration time:
	+ Console - 1 minute to 12 hours
	+ SDK or CLI - 1 minute to 7 days.
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
+ **Can be associated with a single bucket only**.
+ Use cases:
	+ Grant **public access** to the bucket.
	+ Force objects to be encrypted at upload.
	+ Grant access to another account.
+ S3 Block Public Access settings override bucket policies.
### **When to use**
+ To enforce global policies on all objects in a bucket.
+ For simple cross account access like providing read-only access to entire bucket.
## S3 Access Points
+ Simplifies access management for data in shared buckets.
	+ Segregate into groups and provide required access.
+ An access point has a DNS name and ARN, which is used in place of bucket ARN.
+ Has its own access policy that grants permissions to users/applications.
	+ Simplifies bucket policy
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_33934122.png]]
+ **Can be associated with a single bucket only**. (bucket can be in different account also)
+ Can be configured to be private (access only within VPC) or public.
	+ If private, access from outside VPC using a [[Amazon VPC#VPC Endpoints| Gateway VPC endpoint]] . 
	+ VPC endpoint policy must allow access to target bucket and access point.
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_33934122 (1).png]]
### **When to use**
+ Large shared bucket with data that many teams/applications need, with different permissions.
+ Need to easily onboard/offboard teams/applications without modifying bucket policy and risking unintended consequences.
## Bucket policy vs Access Points
| Feature             | S3 Bucket Policy                                                                                                                                                                   | S3 Access Point                                                                                                                                                                       |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Scope**           | Applies to an entire bucket and all its contents. It provides a single, resource-based policy that governs access for everyone.                                                    | Attached to a specific bucket but provides a unique endpoint with its own access policy. You can have thousands of access points for a single bucket, each with distinct permissions. |
| **Scalability**     | As the number of applications or teams grows, a single bucket policy can become very large and complex to manage. A misconfiguration can affect all applications using the bucket. | Highly scalable and designed for managing access to large, shared datasets. You can create a separate, simplified access policy for each individual application or team.              |
| **Permissions**     | A change to the bucket policy can have wide-ranging effects on all applications accessing the bucket.                                                                              | Changes to one access point policy only affect the applications that use that specific endpoint, limiting the "blast radius" of a potential misconfiguration.                         |
| **Network Control** | Can restrict access based on IP addresses, but doing so for a large, shared bucket is difficult to manage effectively.                                                             | Can be restricted to a specific Virtual Private Cloud (VPC), which provides powerful network-level isolation for internal applications.                                               |
| **Authorization**   | The bucket policy is the single source of truth for authorization at the bucket level.                                                                                             | The final access decision is the _intersection_ of both the access point policy and the bucket policy. The most restrictive "deny" or "allow" takes precedence.                       |
| **Access Method**   | Applications and users access the bucket directly using its name or ARN.                                                                                                           | Applications and users access the bucket indirectly through the access point's unique ARN or alias.                                                                                   |
## S3 Object Lambda
+ Use [[AWS Lambda]] functions to change an object before it is retrieved by caller application.
+ _S3 Object Lambda Access Point_ is created which internally uses a standard S3 Access Point (called supporting access point) and a lambda function.
	+ The object is retrieved using the _supporting access point_.
	+ The lambda function processes it (can add/remove data) and returns transformed object to the _Object Lambda access point_, which in turn returns it to the caller application.
+ Supports `GET`, `HEAD` and `LIST` operations.
+ Lambda function must be in same region as Object Lambda access point
+ Use cases:
	+ Redacting PII information for analytics or non-production environments.
	+ Converting across data formats such as converting XML to JSON
	+ Filtering to return subset of data
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_36228854.png]]
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
## MFA Delete
+ If enabled, MFA required for
	+ Suspend versioning of bucket
	+ Permanently deleting an object
+ Helps prevent accidental deletes.
+ **Versioning must be enabled** to use MFA delete.
+ Only bucket owner can enable/disable MFA delete
	+ Can enable/disable only through AWS CLI, SDK or S3 REST API
# S3 Lifecycle Rules
+ Helps optimize cost of object storage throughout its lifecycle by
	+ Transitioning objects to lower cost storage classes.
	+ Deleting expired objects.
+ Best for data with **predictable data access patterns**.
+ Overrides [[#Bucket policy]].
+ Two types of actions
	+ ***Transition actions***
		+ Define when objects should transition to next storage class.
			+ Move current versions of objects b/w storage classes
			+ Move non-current versions of objects b/w storage classes.
	+ ***Expiration actions***
		+ Define when objects should expire.
		+ S3 deletes expired objects. (adds delete marker if versioning enabled, else permanent delete)
			+ Permanently delete non-current versions of objects
			+ Delete current versions of objects
			+ Delete incomplete multi part uploads
			+ Delete expired delete markers.
+ Rules apply to both **existing** and **new** objects.
+ Rules can be created for
	+ a certain prefix (Ex: s3://mybucket/mp3/*)
	+ certain object tags (Ex: _Department_, _Finance_)
## S3 Storage Class Analysis
+ Helps decide when to transition objects to right storage class based on data access patterns. S3 Analytics does the analysis.
+ Recommendations for [[#**S3 Standard**|S3 Standard]] and [[#**S3 Standard-IA**|S3 Standard IA]] only.
+ Report is updated daily
+ 24-48 h to start seeing data analysis.
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
# S3 Event Notifications
+ Notifies when certain events happen in S3 bucket. Some events are listed below:
	+ Create object
	+ Delete object
	+ Replication
	+ [[#S3 Lifecycle Rules|S3 Lifecycle transition/expiration event]]
	+ [[#S3 Intelligent-Tiering]] automatic archival event
	+ [[#**Reduced Redundancy Storage**|Reduced redundancy storage]] object loss event
+ To enable notifications, add notification configuration
	+ Add events that S3 should publish
	+ Destinations to send notifications. 
		+ [[Amazon SNS]] topics
		+ [[Amazon SQS]] queues
		+ [[AWS Lambda]] functions
		+ [[Amazon EventBridge]]
			+ **All** events are sent to EventBridge. No need to specify events
+ Notifications delivered within seconds, but can take a few minutes.
+ There must be a [[IAM#Policy|IAM Resource Policy]] attached to the destination, that allows S3 to send notifications to the destination.
	+ Amazon EventBridge does not require an IAM policy. 
# Performance optimization
+ S3 autoscales when request rate spikes. Latency 100-200 ms
	+ For lower latency (single digit ms), pair with [[Amazon CloudFront]] or [[Elasticache]] for caching.
+ Limit of 
	+ **3,500 PUT/COPY/POST/DELETE** requests/s per prefix in a bucket
	+ **5,500 GET/[[REST#HEAD|HEAD]]** requests/s per prefix in a bucket.
	+ No limit on number of prefixes in a bucket
+ Can increase R/W performance using parallelization.
	+ If a bucket has 10 prefixes, parallelizing reads increases performance to 55,000 request/s (5,500 GET requests/s per prefix).
+ Multi part upload
	+ recommended for files > 100MB
	+ must use for files >  5GB
	+ Can parallelize uploads to speed up transfers![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_11851522 1.png]]
+ Use S3 Transfer Acceleration for fast, easy and secure transfer of files over long distances. (Ex: uploading file while in California to a bucket in _eu-west-1_) .
	+ File is uploaded to closest [[AWS Global Infrastructure#Edge locations|Edge location]], instead of bucket.
	+ File is transferred along the AWS private network, which is optimized and uncongested to the destination S3 bucket
	+ Compatible with multi-part uploads.
+ Use _byte range fetches_ to request part of an object or to increase download speeds.
	+ HTTP request includes a _Range_ header which specifies the range of bytes to fetch.
	+ Parallelize byte range fetches to reduce download time for an object.
	+ Can restart failed downloads by only fetching byte ranges that are missing.
	+ Can optimize video/audio streaming by fetching partial segments that user is watching, and if user seeks to a later part, can simply fetch for that specific byte range.
	+ Combine with S3 Transfer Acceleration for faster downloads over long distances.
# Object Encryption
## Server Side Encryption with Amazon S3 Managed Keys (SSE-S3)
+ Default encryption method for new buckets and objects.
	+ Bucket policy overrides default encryption
+ Keys handled, managed and owned by AWS
+ AES-256 Encryption
+ Must set header `x-amz-server-side-encryption:AES256` in request, if you explicitly want to use SSE-S3 encryption.
## Server Side Encryption with AWS KMS Keys (SSE-KMS)
+ Uses keys handled by KMS.
	+ Keys can be AWS managed or customer managed.
	+ Key has to be symmetric.
	+ Key must be in same region as bucket.
	+ Access to keys managed through policies
	+ Can audit key usage in AWS CloudTrail
+ Must set header `x-amz-server-side-encryption:awskms` (object level)
+ Can be configured to be bucket-level (bucket-policy) or object-level.
## Server Side Encryption with Customer Provided Keys (SSE-C)
+ Uses key managed by customer **outside of AWS**.
+ Key not stored, must be passed in header of each request. (HTTPS only)
	+ S3 stores salted HMAC value of the key.
+ AES-256 encryption

>[!warning]
>If you lose the encryption key, you lose the object

## Client side Encryption
+ Use client libraries like _Amazon s3 Client Side Encryption Library_.
+ Client must encrypt/decrypt data themselves.
+ Customer fully manages key
# S3 Access Logs
+ If enabled, logs all requests made to an S3 bucket, from any account, authorized or denied to a destination S3 bucket.
	+ Destination bucket must be in the same region as source bucket.
	+ Destination bucket should not have access logging enabled, otherwise there will be a **infinite loop** of logs being delivered to the destination S3 bucket.
	+ Destination Bucket can only be encrypted using [[#Server Side Encryption with Amazon S3 Managed Keys (SSE-S3)|SSE-S3]].
+ S3 uses a special account `logging.s3.amazonaws.com` to write server logs. So destination bucket must grant the principal access using bucket policy (recommended) or ACL.
+ Logs can be analyzed and queried using Amazon Athena

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