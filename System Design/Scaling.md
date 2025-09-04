#system-design #distributed
```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
+ Scaling of application pertains to scaling both **web servers** and **database servers**.
# Why add more machines?
+ **Scalability**: Volume of data is too high or server has too many read/writes
+ **Availability**: If one server goes down, other servers can be used
+ **Latency**: If data center is in America and user request is from India, there will greater latency, so more geographic data centers are added.
# Types of scaling
## Vertical Scaling
+ **Scale-up** type of scaling i.e. adding more power (CPU,RAM, storage) to existing servers.
+ Good for low traffic situations.
+ **Cheaper and easier to maintain**
+ **Hard limit** to how much power can be added to servers.
+ **No failover and redundancy** i.e. if a server goes down or is overloaded, app will be irresponsive or will have a slow response.
## Horizontal Scaling
+ **Scale-out** type of scaling i.e. adding more servers.
+ Good for large applications with heavy traffic
+ **Fault tolerant and available** as failure of one server doesn't mean app is inaccessible.
+ Server overload can be managed using load balancers and careful data distribution ( for database).
+ **More expensive and harder to maintain**
# Load Balancer
+ Distributes traffic between **web servers** equally that are part of load-balancer set
+ The load balancer does health checks for all servers in its sets and maintains a table in which it stores data related to all servers in its set like health info, response time, no of active connections (think TCP).![[Screenshot 2025-04-10 at 5.37.51 PM.png]]
## Advantages
+ Availability: can switch routing to other servers if one is down or overloaded.
+ Scalability: can add in more servers if current ones cannot handle all the load
+ Security: can help mitigate DDoS attacks by routing to other servers instead of compromised one.
+ Performance: By doing all above things, improves performance.
+ Exposes a single point of access to the application. Client doesn't know which server it is connecting to and how many servers exist.
## Load Balancing algorithms
### **Round-Robin**
Sends traffic to servers in its set in rotation.
### **Weighted Round Robin**
Give weightage to servers in set, and traffic routing is based on that weightage. Ex: for 2:1 weightage for a 2 server setup, server A receives twice as much traffic as server B.
### **Least Connections**
Sends traffic to server with least amount of active connections. (Think TCP). Connection info in load balancer table.
### **Least response time**
Sends traffic to server with least response time. Response time in load balancer table
### **URL/ Source IP hash**
Sends traffic based on hash value of url/source ip. hash value is cached by load balancer.
### **[[Consistent Hashing]]**
Requests of a user/network (criteria depends) always mapped to a particular server.
## Types of Load Balancers
### **Network/ Layer 4**
Routes based on source/dest port or IP address.
### **Application/ Layer 7**
Routes based on headers, session data, response.
### **GSLB**
Global server load balancer, routes based on geography i.e. to closest data center and then uses layer 4/layer 7 load balancer to route to appropriate server.
### **Hardware load balancer**
On-premises, physical load balancer. More security, but costly to get and maintain
### **Software load balancer**
on the cloud. less costly, no maintenance
## Failover strategies
+ A single load balancer at a point becomes a singular point of failure. If it fails, app becomes inaccessible even though servers are healthy.
+ To prevent this, we have failover strategies
### **Active active**
+ All load balancers are active and routing requests.
+ If one goes down, others can continue routing, so there is minimal disruption.
+ Server resources are fully utilized.
+ Hard to configure and manage.
### **Active passive**
+ One is actively routing requests, while other is in standby mode, doing health checks.
+ The passive load balancer is synced with active one so it can take over if the active one goes down.
+ Doesn't fully utilize server resources, since one is on standby.
+ There can be temporary service disruption when passive one takes over.
# Database Replication
+ Benefits:
	+ Availability: Data on multiple machines. If one goes down, others are available.
	+ Redundancy: Data (all) present on multiple machines. If one is corrupted, others are still present
	+ Latency: Reduces it, if present in replica machines in multiple geographic locations.
	+ Handle huge reads: by redirecting to replica machines.
+ All data is copied onto replica machines.
## Database Replication strategies
Read [[Database Replication Strategies]] for more detailed info