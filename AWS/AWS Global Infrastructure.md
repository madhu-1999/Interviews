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
# Terminology
>[!info]+
>**Bandwidth**: Amount of data that can be transmitted over the network in a given period of time. Ex: 100 Mbps.
>***Note***: High bandwidth does not imply guarantee faster speed due to other factors such as network congestion,  and quality of your connection.
>**Fully redundant network**: System of duplicate hardware components, software and connections designed to prevent power outages and maintain continuous operation even when failures occur.
>	1.  **Hardware Redundancy:** Involves duplicating physical components like servers, routers, switches, power supplies, and storage systems.
>	2. **Software Redundancy:** Uses multiple applications on different servers or virtual instances to provide backups and load balancing capabilities.
>	3. **Data Redundancy:** Focuses on protecting data through backups, replication, and storing copies in multiple locations
>	4.  **Network Links & Paths:** Creates alternative routes for data to travel using technologies like multiple internet service providers (ISPs), link aggregation, or diverse routing.
>	5. **Geographic Redundancy:** Involves distributing network infrastructure and data centers across multiple physical locations to protect against site-specific disasters or outages.
>	6. **Power Redundancy:** Uses backup power sources like generators or uninterruptible power supplies (UPS) to keep critical network devices operational during power outages.

# Region
+ Physical location containing groups of data centers called Availability Zones (AZ).
+ Each region has a *minimum* of 3 isolated, physically separate AZ's within a geographic area.
+ All regions are connected via a high bandwidth, low latency, fully redundant network.
## Choosing an AWS Region
+ Compliance: Some governments require data to remain local.
+ Latency: Proximity to users ensures lower latency
+ Service Availability: Not all regions have all services.
+ Pricing: Varies by region

# Availability Zone (AZ)
+ Group of data centers within a region.
+ Each AZ has redundant power, network and connectivity.
+ Allows operation of applications and databases that are highly available, fault tolerant and scalable.
+ All AZ's in a region are connected via high bandwidth, low latency, fully redundant network.
+ All traffic between AZ's is encrypted.
+ [[Database Replication Strategies#Synchronous updates| Synchronous replication]] between AZ's.
+ All AZ's in a region are far enough from each other to avoid damage to multiple of them in case of any natural disasters.
# Local Zones
+ Provides compute, storage, database and other select  AWS services closer to end users (Closer than closest AZ in the geographic area).
+ Ex: Real-time gaming
# AWS Outposts
+ Can use AWS APIs, tools and infrastructure on-premises or own data center along with AWS cloud for  [[Cloud Computing#Alternatives|hybrid deployment]].