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
# Storage in Cloud
+ Eliminates need for physical hardware in data center for storage
+ Scale as needed
# Types of Storage in AWS
## Block storage
>[!tldr]
>+ Used for performance critical applications such as VM's and databases.
>+ Offers fast, low-latency data access

+ Breaks data into fixed, equal size pieces called blocks.
+ Each block is assigned a unique id and stored independently, allowing for flexible management of data.
+ This allows for faster read and writes because data can be accessed directly from the blocks.
+ Data can be updated, block by block, meaning the whole file doesn't need to be changed every time you make an update
+ AWS provides block storage volumes that attach to EC2 instances like physical hard drives.
+ AWS offers two primary block storage services:
    + Amazon EC2 Instance store : 
	    + Unmanaged, volatile, high-performance storage ==physically== attached to EC2 instances for temporary data. 
	    + Data is lost if instance is stopped or terminated.
	    + Use for buffers, caches, scratch space for processing.
	    + ==Cannot customize size of instance storage. It is predetermined by the instance type selected==.
	    + Available only for specific instance types. ([[Amazon Elastic Compute Cloud (AWS EC2)#EC2 Instance Types| I, D, H]] )
    + [[Amazon EBS]] : Managed, persistent block storage volumes for EC2.
## File storage
>[!tldr]
>+ Ideal for data that requires shared access like CMS

+ Hierarchical file system accessible over network, so multiple users and applications can share data simultaneously.
+ Access is slower due to its hierarchical nature.
+ Can scale and expand storage as per needs on AWS.
+ Offers two primary file storage services
	+ [[Amazon EFS]]: Fully managed, scalable NFS file system.
	+ Amazon FSx: Fully managed, file storage for popular file systems like Windows, Lustre and NetApp ONTAP.
## Object storage
>[!tldr]
>+ Used to store large amounts of unstructured data such as media files, backups, data archives.
>+ Highly scalable and cost-efficient for huge volumes of data
>+ Best for data that doesn't change frequently.
+ Organizes data into objects, containing the actual data, metadata and a unique id.
+ Objects are organized in flat structures called buckets which eliminates need for folder structures and makes data highly accessible.
+ Requires rewriting the entire object for every change.
+ Metadata makes it easier to manage, search and perform analytics across all objects in storage.
+ [[Amazon S3]]
## Other storage services
+ *AWS Storage Gateway*: Fully managed, hybrid cloud storage that provides on-premises access to unlimited cloud storage
+ *AWS Elastic Disaster Recovery*: Fully managed service that streamlines recovery of your physical, virtual and cloud based servers into AWS.
# Shared Responsibility for Storage services
## Fully Managed Services
### **AWS** 
+ Responsible for everything from hardware and infrastructure, data durability, availability, encryption at rest and replication.

### **User**
+ Responsible for data management, access control and service configuration.
## Managed Services
### **AWS**
+ Manages underlying infrastructure, hardware redundancy and volume replication.
### **User**
+ Responsible for data backup, encryption, volume performance optimization and capacity planning.
## Unmanaged Services
### **AWS**
+ Maintains underlying hardware and network infrastructure.
### **User**
+ Full responsibility for data management, backup/ recovery, encryption, durability etc.. 

# File vs Object vs Block Storage
| Feature             | File Storage                                                                             | Block Storage                                                                                         | Object Storage                                                                                             |
| ------------------- | ---------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| **Data structure**  | A hierarchical tree of files and folders.                                                | Chunks of data broken into fixed-size blocks with unique identifiers.                                 | Individual, self-contained units (objects) with data, metadata, and a unique ID.                           |
| **Metadata**        | Limited, with basic attributes like file name, date, and type.                           | Minimal metadata to maximize speed. The operating system manages block addresses.                     | Highly customizable, extensive metadata that is stored with the data itself.                               |
| **Performance**     | Can be slow to retrieve as the data volume grows due to navigating the folder hierarchy. | High-performance and low-latency, making it ideal for transactional workloads.                        | Lower performance than block storage but offers massive scalability and fast retrieval for static content. |
| **Scalability**     | Less scalable because of the fixed hierarchy. Performance can degrade as data grows.     | Scales by adding more volumes, but scalability depends on the system's ability to handle I/O demands. | Infinitely scalable due to its flat, distributed architecture.                                             |
| **Cost efficiency** | Generally affordable for smaller-scale use, but costs can increase with scale.           | Can be expensive due to the need for high-performance hardware.                                       | Very cost-effective for storing large volumes of data, with a "pay-as-you-go" model in the cloud.          |
| **Access method**   | Accessed via file paths and standard file protocols (e.g., NFS, SMB).                    | Accessed by the operating system, which reassembles the blocks into a file.                           | Accessed programmatically via HTTP-based RESTful APIs.                                                     |
| **Modification**    | Allows frequent, in-place edits to files.                                                | Supports incremental changes by only modifying the affected blocks.                                   | Objects are immutable. Any change requires writing a completely new object.                                |