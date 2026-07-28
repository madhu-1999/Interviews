#database #distributed #system-design #hld
# Horizontal Sharding
+ Splitting a **database** into smaller, manageable chunks called **shards**.
	+ Partitioned data chunk is called **logical shard** and the physical server on which it is stored is called **physical shard**.
		+ A physical shard can store **multiple** logical shards (isolated from each other).
			+ If storing multiple logical shards together, the goal is to ensure all related data is stored on same shard. Ex: all of a user's data (profile, posts, purchases) should reside on the same shard.
	+ Data is distributed such that a row appears in exactly one shard.

>[!note] A logical shard is a [partition](Database%20Partitioning.md).

![Screenshot 2025-09-25 at 11.01.22 PM](Assets/Screenshot%202025-09-25%20at%2011.01.22%20PM.png)
+ [](Scaling.md#Horizontal%20Scaling|Horizontally%20scales) a database to improve **throughput** and **storage capacity**. 
# Advantages
+ **Horizontal write scaling** : Write operations are distributed across multiple shards, instead of one server handling all the writes, increasing write capacity.
+ **Unlimited storage capacity**: Add more shards when needed, can infinitely scale out.
+ **Improved query performance**:  Queries run faster on shards since there is less data to scan.
+ **Fast backups**, since we can parallelly backup each shard, reducing the time.
## Disadvantages
+ **Operational overhead**: More complex to monitor, manage and backup multiple shards than a single database.
+ **Cross shard queries** i.e. queries that join data on multiple shards are slower and more complex to handle.
+ **No data durability and low availability** if shard is not replicated or backed up.
# Sharding strategies
+ Data is partitioned on the basis of a unique **shard key** i.e. each shard has a unique shard key which acts as a identifier for the shard.
## Range Based Sharding
+ Data is partitioned based on a **contiguous range of values** for a chosen key.
	+ Ranges are non-overlapping.
	+ Key should not be monotonic i.e. non-increasing or non-decreasing.
		+ Monotonic key can be used as part of a composite key
+ Related data, based on shard key is stored together. (data locality)
![Screenshot 2025-09-26 at 12.05.59 AM](Assets/Screenshot%202025-09-26%20at%2012.05.59%20AM.png)
### **Advantages**
+ **Efficient for range queries** as well as individual record lookups.
+ Simple to implement if ranges are fixed.
### __Disadvantages__
+ If chosen key has low cardinality (unique values), cannot create more granular ranges .i.e. no of shards becomes limited.
+ If chosen key has high frequency of repeating values, all of them will end up in the same shard. This can create a _hotspot_ if there are a lot of reads/writes for these values.
+ If ranges are not chosen carefully, some shards may end up larger than the others, leading to **uneven distribution**. Ex: From above diagram, Shard 1 is larger.
+ A **monotonic key**, like a timestamp or an auto-incrementing ID, will cause all new writes to be sent to a single shard at the end of the range. This creates a severe write-heavy _hotspot_, while the other shards sit idle.

## Key Based Sharding
+ Value of chosen column(s) is passed through a hash function and data is partitioned based on hash value obtained.
	+ Use same column across all data to partition data consistently.
	+ Should have **high cardinality** i.e. greater number of unique values and **low frequency** of repeated values
![Screenshot 2025-09-25 at 11.22.44 PM](Assets/Screenshot%202025-09-25%20at%2011.22.44%20PM.png)
### **Advantages**
+ A good hash function ensures **uniform and random distribution** of data across shards, preventing any _hotspots_ that can lead to heavy write or read traffic.
+ Works well with keys that change monotonically i.e. in one direction only, either non-decreasing or non-increasing.
+ Works best if majority of queries are individual record lookups, since data is partitioned randomly.
### **Disadvantages**
+ **Inefficient range queries** since related/ranged data is scattered. Would need to perform a _fan-out_ query, broadcasting to all shards and merging the results, which is slower.
+ Uneven distribution of data if shard key has low cardinality (few unique values) and more repeated ones, since all repeated data will have same hash value and thus, be in the same shard. This can create _hotspots_.
+ Difficult to change hash function, as we would have to reshard the entire database.
+ Choice of hash function is crucial to getting a even distribution.
## Directory-based sharding
+ A central **lookup table** stores a mapping of each shard key value and its shard.
+ When request is made, first the lookup table is queried to find the correct shard and then request is routed to that shard.
![](https://miro.medium.com/v2/resize:fit:1400/1*RDozOr6i2a9pA79brBZf5g.png)
### **Advanatages**
+ **Flexible data distribution** since lookup table can dynamically update the lookup table to change mappings.
+ **Easier to rebalance shards**, since only the lookup table needs to be updated.
+ Simple and easy to route queries to correct shards.
+ If a certain key value is queried frequently, isolating all its related data to one shard **improves query performance**. It can also be scaled independently to handle greater reads/writes
## **Disadvantages**
+ **Single point of failure** 
+ **More latency** since we have to query lookup table first.
+ If a certain shard key value has high repeat frequency, can lead to **uneven distribution of data**
## Geo sharding
+ Distribute data across multiple physical shards based on geographic location.
	+ Shard key could be geolocation or IP address.
![Screenshot 2025-09-26 at 2.04.28 PM](Assets/Screenshot%202025-09-26%20at%202.04.28%20PM.png)
### **Advantages**
+ **Reduced latency** since requests are routed to nearest shard.
+ Helps companies **comply with data residency laws**, such as Europe's GDPR, by ensuring that user data is stored and processed within its country or region of origin.
+ If one geographic shard or data center fails, only the users in that specific region are affected. 
## **Disadvantages**
+ Determining the correct geographic boundaries and user-to-shard mapping can be difficult.
+ Population density can lead to _hotspots_, where one shard (e.g., for a major city) handles significantly more load than others
+ Cross shard queries (ex: global analytics) require data from multiple regions, and negate the latency benefits.
+ When a user travels and crosses a geographic boundary, the system must handle the complex and non-atomic process of migrating their data to the new shard, which can lead to temporary data inconsistencies.
+ The data needs to be replicated to a different region for redundancy. Transferring data between regions incurs network transfer costs & leads to higher operational expenses.
# Pros and cons to placing multiple logical shards on a single server
## Pros
+ For small-scale applications, it is more cost-effective to place multiple shard on one server. Leads to better resource utilization since activity is also less for a single shard.
	+ If a shard becomes too large or active, easy to move it to its own server.
+ Can strategically place a ___hot___ shard along with several ___cold___ shards to balance the workload on a server.
## Cons
+ If shards are not distributed properly, can lead to resource contention between shards and impact performance.
+ If server fails, multiple shards are lost. Without proper backup or replication in place, it can lead to data loss and impact availability.
+ A single server has resource limitations. At some point, will have to move shards to dedicated servers to continue scaling.
	# Vertical Sharding
+ Splitting data by functionality i.e. splitting columns of a table in different shards instead of rows.
	+ Primary key of table is used to identify related data across shards.
	+ A subset of **all rows** are stored on a shard
+ Use when table columns can be logically separated into distinct subsets.
![](https://miro.medium.com/v2/resize:fit:1400/1*SQ1x-NHaTCDDjedix4YaCA.png)
## Advantages
+ **Improved query performance** for queries that request a **subset of columns**.
+ **Reduced write contention** since writes are directed to specific shards rather than entire table. (Some columns may be updated more frequently than others.)
+ If a subset of data experiences more reads or writes, separating it into its own shard means we can scale it independently. More efficient and cost effective than scaling entire database.
## Disadvantages
+ **Limited scalability**, as it does nothing to scale database storage capacity. Would eventually need to apply horizontal sharding on top.
+ Adds **operational overhead** to monitor and backup a table across shards.
+ Need to perform **cross shard join** just to query data which was originally part of one table. Ex: Get customer name with transaction details.
# How to route requests to correct shard
+ Assuming shard key is part of request
## Implement logic on client application
+ Based on knowledge of sharding strategy used, use shard key to figure out which shard the request should be routed to.
+ **Less latency** compared to using a routing layer
+ **More operational overhead** since client application has to decide where to route request to.
## Use a routing layer (Recommended)
+ Typically routing layer is a [](Proxy%20server.md#Database%20Proxy|database%20proxy).
+ Separates database layer entirely from application.
	+ Client application forwards request to routing layer which is responsible for routing to the correct shard based on shard key and its knowledge of the sharding scheme.
+ Can detect failed shards and redirect to healthy replicas, minimizing downtime.
+ Performs a _fan out_ query i.e. broadcast requests to all shards if the request does not include the shard key.
+ **More latency** since request has to pass through an extra layer instead of directly to the shard.
