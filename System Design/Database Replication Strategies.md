#system-design #database #distributed 
```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
# Prerequisite
[[Scaling#Database Replication|Database Replication]]
[[ACID]] 
# Single Leader
+  Implemented as a master slave model, where master db handles all the writes and some reads and slave/replica db's handle only the reads.![[Screenshot 2025-04-11 at 11.56.59 AM.png]]
+ On receiving a write request, the master db updates its own data and then sends a update request to its followers who, upon receiving the update request, update their own data.
+ All writes down by master are recorded in a replication log which is then sent to the slaves for update requests.
+ Updates can be sent in either a **synchronous** or **asynchronous** manner.
+ Good for read heavy workloads
+ Write heavy workloads can cause leader bottleneck.
## Synchronous updates
+ Response to user is returned **only** after all the slave db's acknowledge that they have processed the update request.
+ Slower, since we have to wait for acknowledgement from all slaves.![Depiction of Synchronous replication. Follow the numbers written along with the step to understand the relative ordering of operations.](https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Fgfdhn64xemhqviqh90mz.png)
## Asynchronous updates
+ Response to user is returned after master db is done processing it. the replicas receive and update their data in the background. The system will eventually be consistent.
+ Faster since we do not wait for slaves to finish their updates.
+ Reads can be stale if slaves are behind the master.![Depiction of Asynchronous replication. Communication between leader and followers is performed asynchronously.](https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Fnj0tg3h4hi9e7x2lwigd.png)
## Semi-synchronous replication
+ In practice, one slave is updated synchronously while others update asynchronously.
+ Ensures, there is atleast one slave that can be promoted to master if master goes down.
## What happens if slave db go down?
+ If other slave db's are available, they handle all the reads until new slave db comes up.
+ In case all slave db's are down, master db takes over reads temporarily, until atleast one slave db comes back up.
+ When the slave db comes back up again, it asks the master for updates since the time it went down. It knows last update processed, since it keeps its own log of update requests processed. After it becomes consistent, it handles reads again.
## What happens if master goes down?
+ Leader health check (ex: heartbeat) is done periodically.
+ If master goes down, a slave db is promoted to master db, and a new slave db is replicated to take over for the old one.
+ When old master comes back up, it should recognize it is a slave now and receive updates from new master to become consistent before handling reads.
# How to add new slaves?
### **First method**
+ Stop processing writes to the master, copy data to new slave and resume.
+ Not preferred, since writes are blocked until new slave comes up.
### **Second method**
+ Latest snapshot of master is copied to new replica, then the replica asks the master for updates since the snapshot using sequence numbers in snapshot and master's replication log.
+ Once done, it starts operating as a slave and handles read requests and receives updates requests from master like the other slaves.
# Issues arising from replication lag
+ Can occur in asynchronous/ semi-synchronous update systems.
### **Read your own write consistency**
+ For example let’s say, you have updated social media profile. Update will go to leader and you just refreshed the page to see updated profile. But if your read requests goes to a follower where replication wasn’t completed, you’ll see old profile.
+ Some ways this can be resolved:
	+ Master responds to such read requests, until a certain "buffer" time since write request.
	+ Monitor replication lag on slaves and prevent queries on them until they are only lagging behind a threshold amount.
	+ Client keeps timestamp, and only those followers respond to query that are ahead or updated up to the timestamp.
### **Monotonic reads**
+ Case where user reads from a replica that is up to date but then again reads same data from a lagging replica, receiving data that is stale.
+ **Monotonic reads** guarantees a solution, involves a user always reading from a given replica. This, however has a drawback where availability is compromised if the node goes down.
### **Consistent prefix read**
+ If user A writes 50 to a object using L1 leader, then L1 leader returns success and initiates update to L2 and L3 leaders. Assuming L3 receives and updates value to 50, but L2 update is still pending. A new user B requests write to same object to 70 from L3 leader. L3 leader returns success, and initiates update requests to L2 and L1. L2 updates new value 70 first and then 50, which leads to **inconsistent** state.![[Screenshot 2025-04-11 at 4.14.59 PM.png]]
+ Version vectors are one solution to this problem.
# Multi Leader
+ Updates are aysnchronous i.e. consistency is weaker.
## Multi-Data Center
+ One leader in each data center. Within data center, it is a single leader.
+ Leaders in each data center, synchronize with each other periodically.
+ Lower latency than single leader since setup is in multiple data centers.
+ Not suitable when strong consistency is required, since data is concurrently modified in different datacenters.
![What is Multi-Leader Data Replication Strategy? | by James Kwon | Medium](https://miro.medium.com/v2/resize:fit:1356/1*jhTTDAX0gh_V94QsN2jTJg.png)
## Offline mode support
+ Local single leader setup, which handles writes and reads when app is not connected to internet.
+ When it connects, the leader will sync with other datacenter or cloud residing leader to maintain consistency.![Offline Mode Support with Multi Leader Replication](https://miro.medium.com/v2/resize:fit:1400/1*KpcQZ47wkPI-7yiWXWry0A.png)
## Issues in multi-leader setup
### **Write conflicts**
+ When two leaders receive a write request concurrently for the same piece of data, but with different values. Since it is asynchronous update, both will give the client a positive response.The conflict is detected by the replica during update.
+ Some ways to resolve:
	+ Both leaders should store timestamp of request/ keep unique id and forward during update request. Replica keeps the value with latest timestamp/ higher id.
	+ All writes for a particular piece of data goes to a particular leader. (Preferred)
	+ Merge both writes together and store that value. Ex: shopping cart state
	+ Application prompts user to resolve conflict.
### **[[Database Replication Strategies#Issues arising from replication lag | Replication lag issues]]**
## Multi-leader topologies
+ Topologies for connecting all the leaders.
### **Circular**
+ default in Mysql
+ Same as [[Network Topologies#Ring | Ring Topology]]
### **Star**
+ Same as [[Network Topologies#Star|Star]]
### **All to all**
+ Same as [[Network Topologies#Mesh|Mesh]]
+ Preferred approach
+ Suffers from [[Database Replication Strategies#Issues arising from replication lag#Consistent prefix read|Inconsistent prefix read]]
# Leaderless
+ Used by Amazon DynamoDb, Cassandra 
+ Clients directly write to replicas. No leaders
+ Better availability since if one goes down, others are still able to process reads and writes.
+ Better fault tolerance since if one goes down, it doesn't affect other nodes unlike other setups where if leader went down, recovery process is longer.
+ Offers Strong consistency through [[Database Replication Strategies#Leaderless#Quorum read and writes|quorum read and writes.]] 
## Client-driven fan-out
+ Write request is sent to all replicas. Client waits for acknowledgement from a majority of replicas(i.e. if $n$ total nodes,$n/2+1$ nodes) to consider operation a success.![Write Operation in Leaderless Replication](https://user-images.githubusercontent.com/4745789/148634243-36259bc6-ee6e-4fd2-b399-c475ad7a405d.png)
+ Read request is sent to all replicas. Client waits for response from majority of nodes (i.e. for $n$ nodes, $n/2+1$ nodes) and then returns value with latest version/highest id.![Read Operation in Leaderless Replication](https://user-images.githubusercontent.com/4745789/148634242-adf09717-a063-455f-b5d2-57497e60ca27.png)
## Node coordinator
+ Client sends read or write request to node coordinator which does [[Database Replication Strategies#Leaderless#Client-driven fan-out|client driven fan out]].
+ makes it easier for clients since fan out responsibility is on node coordinator.
![Node Coordinator Driven Fan-out in Leaderless Replication](https://user-images.githubusercontent.com/4745789/148634240-0c05e68f-ec35-4ce8-8053-e7334f616dd6.png)

## Quorum read and writes
+ For **strong consistency** $w+r > n$ :
	+ $w$: no of nodes that confirm write with ack
	+ $r$: no of nodes queried for data
	+ $n$: total no of nodes
+ set of nodes used in $w$ and $r$ ***CANNOT*** be mutually exclusive and ***MUST*** have atleast one overlapping node. This ensures atleast one node has the latest value written.
## Preventing stale reads
+ When a node that went down, comes back up, it will have stale data.
### **Read Repair**
+ When client reads from multiple nodes, and sees that some node has stale data, it writes newer data to the stale one. Version numbers are used to differentiate with latest (higher version number) and stale data (lower version number).
![](https://github.com/jguamie/system-design/raw/master/images/replication-10.png)
### **Anti Entropy**
+ Background process that periodically checks for discrepancies between replicas and corrects them.
+ Writes are not copied in order, and there may be delay before it is copied.
+ Without an anti-entropy process, values that are rarely read would be missing in some replicas. This affects [[ACID#Durability|durability]] of database as it would only become consistent when [[Database Replication Strategies#Leaderless#Preventing stale reads#**Read Repair**|read repair]] is performed.
