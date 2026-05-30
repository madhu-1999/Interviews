#aws #cloud
# Prerequisite
+ [[IP addressing]]
# Overview
+ It is a logically isolated virtual network. (bound to a [[AWS Global Infrastructure#Region|region]]) 
+ Subnet (range of IP addresses) allows for partitioning of a VPC (bound to a AZ)
	+ Public subnet is accessible from the internet.
	+ Private subnet is not accessible from the internet. (recommended)
	+ One AZ can have multiple subnets.
	+ AWS resources deployed inside subnets
![A VPC with an internet gateway and subnets in three Availability Zones.](https://docs.aws.amazon.com/images/vpc/latest/userguide/images/how-it-works.png)

+ Gateway connects a VPC to another network.
	+ Internet gateway connects public VPC to internet
	+ VPC endpoint to connect to AWS services privately. (public or private VPC)
	+ NAT gateway (AWS managed) or NAT instances (Self-managed) to connect private VPC instances to the internet, while remaining private.
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_19729022.png]]
+ Routing tables used to route network traffic from/to subnet or gateway.
	+ Each subnet must have one, and is associated *by default* to main route table in VPC.
# Private IPv4 addresses
+ When any AWS resource instance, like [[Amazon Elastic Compute Cloud (AWS EC2)#EC2 Instance|EC2 instance]] is launched in a VPC, a private IPv4 address is assigned to the its primary network interface (ex: eth0).
+ Every instance also gets a private DNS hostname that resolves to the private IP address.
```
IP address Domain name 
   ↓--------↓ ↓------------------------↓ 
ip-10-24-34-0.us-west-2.compute.internal 
↑-----------↑ Hostname 
↑--------------------------------------↑ Fully qualified domain name (FQDN)
```
+ It is always part of the subnet's CIDR block. 
# Public IPv4 addresses
+ If subnet is public, AWS resource instance, is assigned a public IP to the primary network interface (ex: eth0).
	+ Assigning public IP to instance on launch can be overriden by a feature flag during instance launch configuration.
+ AWS charges for all public IPv4 addresses. 
+ It is assigned from a pool of public IP addresses, not associated with the VPC.
	+ When instance is stopped/terminated, the public IP is released back into the pool. This is why public IP of instances are dynamic.
+ If enabled, instance receives a public DNS hostname that resolves to 
	+ public IP address, if outside VPC
	+ private IP address, if inside VPC
## Elastic IP
+ Static public IP address
# VPC CIDR blocks
+ Recommended to specify from below private IPv4 address ranges:

| RFC 1918 range                                    | Example CIDR block |
| ------------------------------------------------- | ------------------ |
| 10.0.0.0 - 10.255.255.255 (10/8 prefix)           | 10.0.0.0/16        |
| 172.16.0.0 - 172.31.255.255 (172.16/12 prefix)    | 172.31.0.0/16      |
| 192.168.0.0 - 192.168.255.255 (192.168/16 prefix) | 192.168.0.0/20     |
+  Can specify a publicly routable CIDR 
+ Allowed block size between `/16` and `/28` netmask
+ Can't specify given CIDR blocks:
	+ 0.0.0.0/8
	+ 127.0.0.0/8 (localhost)
	+ 169.254.0.0/16 (link-local)
	+ 224.0.0.0/4 (multicast)
# Subnet CIDR blocks
+ Subset of VPC CIDR block
+ Two subnets in the **same** VPC cannot have overlapping CIDR blocks.
	+ Two subnets in **different** VPC can
	+ Can differentiate between instances with same private IP using public/elastic IP or DNS hostnames.
+ **First 4 IP addresses** and **last IP address** in subnet CIDR block are reserved.
# Default VPCs
+ Each region comes with a _default_ VPC. The default VPC has:
	+ `172.31.0.0/16` CIDR block
	+ public subnet in each AZ of size `/20`  (default)
	+ an internet gateway 
	+ A route in the main route table that points all outbound traffic to the internet gateway.
	+ A default [[#Security Groups|security group]]
	+ A default [[#Network access control lists|NACL]]
	+ DNS settings that assign public DNS hostnames to public IP addresses.
# Ports to know
1. 22 - SSH (log into linux instance)
2. 21 - FTP (upload files to file share)
3. 22 - SFTP (upload files using SSH)
4. 80 - HTTP (access unsecured websites)
5. 443 - HTTPS (access secured websites)
6. 3389 - RDP (log into Windows instance)
# Security Groups
+ Control the traffic that is allowed into or out of the resources that it is associated with. Acts as a virtual firewall.
+ A VPC has a *default* security group. Can add more security groups to a VPC.
+  By default:
	+ All ***inbound*** traffic is ***blocked***
	+ All ***outbound*** traffic is ***allowed***
+ Security group in a VPC can be associated ***only*** with resources in the same VPC. 
+ A resource (say, EC2 instance) can have ***multiple*** security groups.
+ Only contain *ALLOW* rules.
+ Can regulate
	+ Access to ports
	+ Protocol
	+ Authorized IP ranges - IPv4/IPv6
	+ Control inbound traffic through ***inbound*** rules (coming to resource).
	+ Control outbound traffic through ***outbound*** rules (going out of resource).
+ Rules can reference by IP or by security group
	+ Reference by IP means only resources with provided IP or IP in given IP range can send inbound traffic / receive outbound traffic .
	+ Reference by security group means only resources associated with given security group can send inbound traffic / receive outbound traffic.
		+ Inbound rule Security group reference only if atleast one condition is met:
			+ Security groups are part of same VPC
			+ Peer connection b/w VPC's of security groups
			+ Transit gateway b/w VPC's of security groups
		+ Outbound rule Security group reference only if atleast one condition is met:
			+ Security groups are part of same VPC
			+ Peer connection b/w VPC's of security groups
+ If resource is not accessible **(time out)** , then it is a **security group issue**.
+ If resource gives **"connection refused"** error, then it is **resource issue**.
## Naming Rules
+ Must provide a name and description.
+ Name must be ***unique*** within a VPC.
+ ***Cannot*** start with `sg-`
- Names and descriptions are limited to the following characters: a-z, A-Z, 0-9, spaces, and ._-:/()#,@[]+=&;{}!$*. (up to 255 chars)
## Exceptions to Rules
+ Security groups do not filter traffic to or from:
	+ Amazon DNS
	+ Amazon DHCP
	+ Amazon EC2 instance metadata
	- Amazon ECS task metadata endpoints   
	- License activation for Windows instances
	- Amazon Time Sync Service
	- Reserved IP addresses used by the default VPC router
# Network Access Control List
+ Control traffic to and from the subnet.
+ Can have *ALLOW* and *DENY* rules for both ***inbound*** and ***outbound*** traffic.
+ Attached to a subnet
	+ Each subnet must have one. If not specified, a *default* one is attached.
+ Rules only include IP addresses
+ **One NACL => multiple subnets**, but **one subnet => one NACL**
+ *Stateless* i.e. don't save any information about previously sent or received traffic.
+ *Default* NACL **allows all** inbound and outbound traffic.
	+ Cannot add/delete default *numbered* rules.
	+ Cannot delete * rules. (* rules deny traffic if they do not match any numbered rules)
+ *Custom* NACL **denies all** inbound and outbound traffic.
+ Example: *default* NACL

|Rule #|Type|Protocol|Port range|Source|Allow/Deny|
|---|---|---|---|---|---|
|100|All IPv4 traffic|All|All|0.0.0.0/0|ALLOW|
|101|All IPv6 traffic|All|All|::/0|ALLOW|
|*|All traffic|All|All|0.0.0.0/0|DENY|
|*|All IPv6 traffic|All|All|::/0|DENY|
 ![A VPC with two subnets and a network ACL for each subnet.](https://docs.aws.amazon.com/images/vpc/latest/userguide/images/network-acl.png)
## Exceptions to Rules
 + Network ACLs do not filter traffic to or from:
	+ Amazon DNS
	+ Amazon DHCP
	+ Amazon EC2 instance metadata
	- Amazon ECS task metadata endpoints   
	- License activation for Windows instances
	- Amazon Time Sync Service
	- Reserved IP addresses used by the default VPC router
# Security Group Vs NACL
| Feature                    | Security Group (SG)                                                              | Network Access Control List (NACL)                                                       |
| -------------------------- | -------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| **Layer of protection**    | Instance level (network interface).                                              | Subnet level.                                                                            |
| **Stateful vs. Stateless** | **Stateful**: Return traffic is automatically allowed.                           | **Stateless**: Requires explicit rules for both inbound and outbound traffic.            |
| **Rules**                  | Supports **allow** rules only; implicit denial for anything not allowed.         | Supports both **allow** and **deny** rules.                                              |
| **Rule processing**        | Evaluates all rules; order does not matter.                                      | Processes rules in number order and applies the first match.                             |
| **Association**            | Can be associated with one or more instances; an instance can have multiple SGs. | A subnet can be associated with only one NACL, which applies to all instances within it. |
| **Default behavior**       | Denies all inbound, allows all outbound.                                         | Default NACL allows all traffic; custom NACL denies all traffic until rules are added.   |
# VPC Peering Connection
+ Connect two VPCs using IPv4/IPv6 private addresses.
	+ VPC's can be in same/different accounts/regions
	+ No overlapping CIDR blocks between connecting VPCs
	+ Connection is **not transitive**. (Use [Transit Gateway](https://docs.aws.amazon.com/whitepapers/latest/building-scalable-secure-multi-vpc-network-infrastructure/transit-gateway.html) instead)
+ Does not use gateway or VPN connection to connect VPCs.
+ Two way communication
+ Access:
	- **Full access:** If you add a route to the entire CIDR block of the peer VPC, all resources in your VPC can potentially communicate with all resources in the peer VPC (if allowed by other security rules).
	- **Partial access:** To restrict communication, you can specify more granular routes in your route table. For example, you can limit access to a specific subnet, multiple subnets, or even a single IP address in the peered VPC.
![A VPC peering connection](https://docs.aws.amazon.com/images/vpc/latest/peering/images/peering-intro-diagram.png)
## Working
+ _Requester_ VPC sends peering connection request to _accepter_ VPC 
+ _Owner_ of _accepter_ VPC must accept request to activate connection.
+ A route must be added to both VPC's route tables, that points to the other VPC's IP address range.
+ Update security groups to allow inbound/outbound traffic to resource from the other VPC.
# VPC Endpoints
![Service consumers create VPC endpoints to connect to endpoint services and resources hosted by providers.](https://docs.aws.amazon.com/images/vpc/latest/privatelink/images/privatelink-concepts.png)
+ Private one-way connection to a specific service in another VPC using VPC endpoint.
	+ VPC containing necessary service is called _service provider_
	+ The service we connect to is called _endpoint service_.
		+ It can be a service within same account, in a different account or hosted on-premises (non-AWS).
+ VPC endpoint is created within _service consumer_ VPC.
	+ Resources in _service consumer_ can connect to _endpoint service_ through VPC endpoint.
	+ A _endpoint policy_ ([[IAM#Policy|IAM policy]]) is attached to it, which determines which _principals_ can use the VPC endpoint to access the _endpoint service_.
+ All traffic between _service consumer_ and _endpoint service_ stays within the AWS private network. It **does not** traverse the internet.
+ Provides **enhanced security** and **lower latency**.
## VPC Endpoint Types
### **Interface**
>[!note]
>+ A _endpoint network interface_ is a AWS managed network interface that provides an entry point for traffic going to an _endpoint service_.
>	+ Automatically created in _service consumer_ when a VPC endpoint is created.

+ Creates a endpoint network interface to send traffic TCP/UDP traffic to endpoint service.
### **Gateway**
+ Connects only to [[Amazon S3]] and [[Amazon DynamoDB]]
+ On creation, must add a new entry to route tables of subnets of _service consumer_ that will use it.
	+ _endpoint service_ prefix list is destination and gateway endpoint as target.
+ Communication strictly within __same VPC__.
	+ Cannot access from on-premises, another VPC or AWS region
>[!note]
> + Prefix list is a range of IP addresses of services that can be referenced to allow/deny access in security groups/route tables.
> 	+ Whenever IP address changes, prefix list is automatically updated, so no need to update manually.
# AWS Client [[VPN]]
+ Managed, client-based service. (OpenVPN-based)
+ Securely access AWS and on-premises services
+ Dynamically scales according to number of users connecting to resources.
+ Supports multiple authentication methods. (ex: Active directory, federated authentication)
+ Supports multiple authorization methods (ex: network access control list, security group based access control)
# AWS Site-to-Site [[VPN]]
+ Connect on-premises private network to AWS. (encrypted)
+ Communication goes over the **public internet**.
 ![[Screenshot 2025-09-18 at 5.30.34 PM.png]]
# AWS Direct Connect
+ Dedicated **physical** connection between your network and VPC.
+ Private, secure and fast.
+ Goes over private network. 
![[Screenshot 2025-09-18 at 5.33.50 PM.png]]
# VPC Flow logs
+ Log IP protocol traffic coming to/from network interfaces in VPC.
	+ Can log *accepted* traffic, *rejected* traffic or *all* traffic.
+ Helps to monitor and troubleshoot connectivity issues.
+ Can create flow logs for AWS managed network interfaces like:
	+ [[Amazon ELB]]
	+ [[Amazon RDS]]
	+ [[Elasticache]]
	+ NAT gateways
	+ Transit gateways
+ Can be published to: [[Amazon S3]], [[AWS CloudWatch]] or Amazon Data Firehose
