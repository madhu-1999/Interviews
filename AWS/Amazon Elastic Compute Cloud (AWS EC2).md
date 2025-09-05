#cloud #aws 
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
# Prerequisite
+ [[Container#Contrast with VM|Virtual Machines]]
+ [[Scaling]]
# Overview
+ *Compute* refers to processing power.
+ *Compute in the cloud* refers to creating virtual machines with a cloud provider to run applications and tasks over the internet.
+ In the cloud, *compute* is available on-demand, can be accessed remotely without owning or maintaining physical hardware.
# EC2 Instance 
+ It is a VM in the cloud. VM's share a physical host machine with multiple other instances.They are all isolated from each other but share host resources. This is called **multi-tenancy**. The isolation and resource sharing is handled by the hypervisor.
+  ==User only pays for running instances, not stopped or terminated ones.==
+ ==Bound to a specific AZ.==
## Steps to create an EC2 Instance
+ First select an Amazon Machine Image (AMI), which is a preconfigured template for the EC2 instance, including all the required components to run (OS, architecture type, additional software, etc).
>[!note]+
>One AMI can be used to launch multiple EC2 instances with the same setup.
+ Then Instance type is selected which allows customization of:
	+ Compute power and cores
	+ RAM
	+ [[Storage in AWS|Storage space]]
		+ [[Network Attached Storage (NAS)]]: ([[Amazon EBS|EBS]]and EFS)
		+ Hardware: ([[Storage in AWS#Block storage|EC2 Instance Store]])
+ Next, we need to create a key pair for SSH login. The public key will be injected into the EC2 instance and the user will hold the private key.
+ Then we define network settings
	+ Network (AWS VPC)
	+ Subnet
	+ Public IP
	+ [[Amazon VPC#Security Groups|Security Group]] 
>[!note]+ 
> By default, public IP address is assigned by AWS and may change whenever the instance is restarted.
+ Configure extra storage or customize existing storage (AMI). 
>[!note]+
>+ AMI Determines size of default [[Amazon EBS|EBS]] root volume.
> + Can increase/decrease default volume size during this step
>+ By default, EBS volume is deleted after EC2 instance is terminated.

+ Specify bootstrap script (if any) : [[#EC2 User data]] 
+ Specify the number of instances to launch
## Amazon Machine Image (AMI)
+ Pre-built virtual machine images that have the basic components for what is needed to start an instance.
+ ==AMI is built for a specific AWS region. Can't launch a EC2 instance using an AMI in  another region.==
+ Three ways to use:
	+ Create [[#custom AMI]]
	+ Use preconfigured AWS AMI's
	+ Buy third-party AMI from AWS marketplace (usually for specialized use cases)
+ Provide **repeatability** if environment is same for every instance. Identical configurations + deployment automation makes testing and dev environments consistent. 
+ Helps reduce errors, scaling and managing large scale environments.
### **Custom AMI** 
+ Start an EC2 instance and customize it.
+ Stop the instance (for data integrity).
+ Build an AMI (Right click on instance -> images and templates -> create image)
+ Launch instances from this custom AMI.
+ If deleted, can be recovered from [[Amazon EBS#Recycle Bin|Recycle Bin]] through retention rules.
## EC2 User data
+ Bootstrap script that runs **only once** when the **instance first starts**
+ Automate tasks such as
	+ Installing updates
	+ Installing software
	+ Downloading files from the internet
	+ Any other setup deemed necessary
+ Runs with the **root** user.
## EC2 Instance Types
![Each EC2 instance type provides a balance of compute, memory, network, and storage resources.](https://docs.aws.amazon.com/images/AWSEC2/latest/UserGuide/images/instance-types.png)
### **Series and Options**
>[!info]+
>|Series|Options|
|---|---|
|- **C** – Compute optimized<br>    <br>- **D** – Dense storage<br>    <br>- **F** – FPGA<br>    <br>- **G** – Graphics intensive<br>    <br>- **Hpc** – High performance computing<br>    <br>- **I** – Storage optimized<br>    <br>- **Im** – Storage optimized (1 to 4 ratio of vCPU to memory)<br>    <br>- **Is** – Storage optimized (1 to 6 ratio of vCPU to memory)<br>    <br>- **Inf** – AWS Inferentia<br>    <br>- **M** – General purpose<br>    <br>- **Mac** – macOS<br>    <br>- **P** – GPU accelerated<br>    <br>- **R** – Memory optimized<br>    <br>- **T** – Burstable performance<br>    <br>- **Trn** – AWS Trainium<br>    <br>- **U** – High memory<br>    <br>- **VT** – Video transcoding<br>    <br>- **X** – Memory intensive<br>    <br>- **Z** – High memory|- **a** – AMD processors<br>    <br>- **b200** – Accelerated by NVIDIA Blackwell GPUs<br>    <br>- **g** – AWS Graviton processors<br>    <br>- **i** – Intel processors<br>    <br>- **m1ultra** – Apple M1 Ultra chip<br>    <br>- **m2** – Apple M2 chip<br>    <br>- **m2pro** – Apple M2 Pro chip<br>    <br>- **b** – Block storage optimization<br>    <br>- **d** – Instance store volumes<br>    <br>- **e** – Extra storage (for storage optimized instance types), extra memory (for memory optimized instance types), or extra GPU memory (for accelerated computing instance types).<br>    <br>- **flex** – Flex instance<br>    <br>- **n** – Network and EBS optimized<br>    <br>- **q** – Qualcomm inference accelerators<br>    <br>- **`*`tb** – Amount of memory for high-memory instances (3 TiB to 32 TiB)<br>    <br>- **z** – High CPU frequency|

### **General Purpose (m)
+ Balance between load, compute, memory and networking resources.
+  Ideal for applications that use these resources in equal proportions such as web servers and code repositories.
### **Compute Optimized (c)**
+ Ideal for processes that require high processing power
+ Ex:  batch processing workloads, media transcoding, high performance web servers, high performance computing (HPC), scientific modeling, dedicated gaming servers and ad server engines, machine learning inference and other compute intensive applications.
### **Memory optimized (R, U, X, Z)**
+ Memory optimized instances are designed to deliver fast performance for workloads that process large data sets in memory.  
### **Accelerated Computing (P, G, Trn, Inf, DL, F, VT)**
+ Use hardware accelerators, or co-processors, to perform functions, such as floating point number calculations, graphics processing, or data pattern matching, more efficiently than is possible in software running on CPUs.  
### **Storage Optimized (I, Im, Is, D, H)**
+ Designed for workloads that require high-disk throughput and low-latency access to large datasets.
Some examples:
![[Screenshot 2025-08-15 at 1.38.21 PM.png]]
Reference for extended reading: https://aws.amazon.com/ec2/instance-types/
# Pricing Options
## On-demand
+ Pay as you go
+ No upfront payments
+ No commitment
## Reserved Instances
+ Upto 75% discount from on-demand
+ 1 or 3 year term commitment
+ Predictable workload using specific instance types and AWS regions
+ Need to reserve specific instance attributes (Instance type, region, os)
## Spot Instances
+ Upto 90% discount from on-demand
+ Bid on spare compute units, which can be interrupted by AWS as necessary
+ No commitment
+ Useful for workloads that are resilient to failure:
	+ Batch jobs
	+ Data analysis
	+ Image processing
	+ distributed workload
## Savings Plan
+ Upto 72% discount
+ 1-3 year commitment
+ Commit to specific usage level over commitment period
+ Locked to a specific instance family and region.
## Dedicated Host
+ Reserve entire physical server for dedicated usage.
+ Ideal for workloads with strict security or licensing needs
## Dedicated Instances
+ Pay for instances running on hardware dedicated solely to user's account.
+ Cannot choose which physical server instances run on.
+ Offers isolation from other AWS customers.

# Auto Scaling Group (ASG)
>[!Info]+
>**Scalability** refers to the ability of a system to handle increased load by adding resources
>**Elasticity** is the ability to scale resources up or down in response to real-time demand.

+ EC2 Auto Scaling adds instances based on demand and key scaling metrics and then decommissions instances when that demand goes down. 
+ It is a collection of EC2 instances, which can scale in and out to dynamically meet demand.
+ Auto-scaling group can launch multiple types of instances, to optimize costs. (Spot + On-demand or Reserved/Savings Plan + On-demand)^[[#Pricing Options]]    
## Launch Template
+ Specify instance configuration information such as AMI, instance type etc.. (All config options in creating EC2 instance).
+ Used by ASG to create new instances.
+ All parameters are optional.
	+ If AMI is not specified, cannot add AMI during ASG creation.
+ If you do not have sufficient permissions to use and create resources specified in a launch template, you receive an error that you're not authorized to use the launch template when you try to specify it for an Auto Scaling group.
	+ Ex: To create EBS volumes with tags from existing snapshots, the user must have read access to the snapshots, and permissions to create and tag volumes.
+ After scaling activity happens, there is a cooldown period (default 300s), during which the ASG will not launch or terminate new instances (to allow metrics to stabilize)
## Size specifications
+ Minimum size: The number of instances never goes below the set *minimum*.
+ Maximum size: The number of instances never goes above the set *maximum*
+ Desired capacity: AWS aims to maintain *desired* number of instances at any given time. If not specified, defaults to *minimum* number of instances.
![A basic architecture diagram of an Auto Scaling group within a VPC.](https://docs.aws.amazon.com/images/autoscaling/ec2/userguide/images/asg-basic-arch.png)
## Scaling approaches
+ **Dynamic Scaling**:  adjusts in real time to fluctuations in demand.
	+  Track a specific CloudWatch metric, and define what action to take when the associated CloudWatch alarm is in ALARM.
	+ Metrics used to invoke the alarm state are an aggregation of metrics coming from all of the instances in the Auto Scaling group.
	+ Target Tracking Scaling
		+ Increase and decrease the current capacity of the group based on a CloudWatch metric and a target value
		+ Ex: Average ASG CPU $\approx$ 40%
	+ Simple Scaling
		+ Single scaling adjustment only.
		+ When CloudWatch alarm is triggered (CPU > 70%) add 2 units (Scale out)
		+ When CloudWatch alarm is triggered (CPU < 30%) remove 1 unit (Scale in)
	+ Step Scaling
		+ Multiple step adjustments possible.
		+ When CloudWatch alarm is triggered ( CPU > 60%) add 1 unit (Scale out)
		+ When CloudWatch alarm is triggered ( CPU > 70%) add 3 units (Scale out)
		+ When CloudWatch alarm is triggered (CPU < 40%) remove 1 unit (Scale in)
		+ When CloudWatch alarm is triggered (CPU < 30%) remove 2 units (Scale in)
	+ For simple and step scaling, 3 types of adjustments to be possible:
		+ Set to exact capacity (=10) if say CPU > 80%.
		+ Add/Remove _x_ units for some metric and utilization.
		+ Add/Remove _x%_ units for some metric and utilization.
+ **Predictive Scaling**: preemptively schedules instances based on anticipated demand.
	+ Ml driven. Forecast based on past data for a metric and target utilization.
+ **Scheduled Actions**: manually schedule instances based on known future demand.
## Health checks
+ EC2 Auto Scaling uses health checks to maintain the *desired* capacity. If any instances are down, it replaces them with new instances. Custom health check can be specified, failure of which leads to the instance being replaced with a new one.
+ If multiple [[AWS Global Infrastructure#Availability Zone (AZ)|AZ's]] specified in the group, instances are divided evenly amongst the AZ's. AWS attempts to create new instances first in the AZ with the least number of instances, on failure moves on to other AZ's. 
> [!note] 
> All AZ's specified ***MUST*** belong to same region. Mulitple regions are not supported
+ If AZ becomes unhealthy , uneven distribution of instances may occur, but EC2 Auto Scaling automatically rebalances when AZ becomes available by creating new instances and then terminating equal amount from other AZ's. 
>[!note]
>If almost near max capacity before rebalancing starts, it can exceed *maximum* threshold by 10% only during rebalancing activity.

## Instance Refresh
+ Use Instance Refresh if we have a ASG using old launch template and we want to:
	+ Replace the launch template with a newer version
	+ Update current EC2 instances with new config 
	+ Keep service running, minimal disruption
+ Need to specify _minimum healthy %_ which tells ASG how many instances can be terminated over time.
	+ As instances are terminated, new ones are launched using the new launch template.
	+ This process continues until all instances with the old launch template are replaced by new instances.
+ Can specify warm-up time i.e. a period needed for an instance to be ready to receive traffic.
##
+ Web app example. Create different auto-scaling group for each tier.
![A basic three tier architecture with an Auto Scaling group.](https://docs.aws.amazon.com/images/autoscaling/ec2/userguide/images/sample-3-tier-architecture-auto-scaling-diagram.png)

Reference: https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-your-first-auto-scaling-group.html
# SSH 
+  download (.pem for MAC/Linux .ppk for Windows) private key, created during EC2 instance creation.
+ Ensure .pem file has read only access only for owner.
```bash
chmod 400 EC2Tutorial.pem
```
+ Ensure security group attached to EC2 instance has a inbound rule allowing ssh on port 22 from anywhere (0.0.0.0/0)
+ Copy public IP of EC2 instance.
+ run command:
```bash
ssh -i EC2Tutorial.pem ec2-user@<public ip>
```
`ec2-user` is created by default on EC2 instance
## EC2 Instance Connect
+ Terminal in browser to SSH into EC2 instance.
+ Only supports Linux based EC2 instances.
+ Uses [[IAM#Policy|IAM policies and principal]] to control SSH access to instances, removing need to manage and share SSH keys.
+ All connection requests through EC2 Instance Connect logged to AWS CloudTrail.
+ Process
	+ When you connect to an instance using EC2 Instance Connect, the EC2 Instance Connect API pushes an SSH public key to the [instance metadata](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html) where it remains for 60 seconds.
	+ An IAM policy attached to your user authorizes your user to push the public key to the instance metadata.
	+ The SSH daemon uses `AuthorizedKeysCommand` and `AuthorizedKeysCommandUser`, which are configured when EC2 Instance Connect is installed, to look up the public key from the instance metadata for authentication, and connects you to the instance.