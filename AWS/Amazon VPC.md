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
# Prerequisite
+ [[IP addressing]]
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
+ [[#Security Group VPC Association feature]] is used to assign a security group to resources in ***other*** VPC's in ***same region***.
+ A resource (say, EC2 instance) can have *multiple* security groups.
+ Only contain *allow* rules.
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
+ Names are not case-sensitive.
+ Name and description upto 255 chars in length.
+ ***Cannot*** start with `sg-`
- Names and descriptions are limited to the following characters: a-z, A-Z, 0-9, spaces, and ._-:/()#,@[]+=&;{}!$*.
- When the name contains trailing spaces, we trim the space at the end of the name. For example, if you enter "Test Security Group " for the name, we store it as "Test Security Group".
## Exceptions to Rules
+ Security groups do not filter traffic to or from:
	+ Amazon DNS
	+ Amazon DHCP
	+ Amazon EC2 instance metadata
	- Amazon ECS task metadata endpoints   
	- License activation for Windows instances
	- Amazon Time Sync Service
	- Reserved IP addresses used by the default VPC router
