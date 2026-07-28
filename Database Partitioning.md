#system-design #database #hld
# Overview
+ Splitting a database into smaller, manageable chunks called partitions.
+ **All partitions remain on the same db server that they were partitioned from**. ![Screenshot 2025-09-27 at 12.07.46 PM](Assets/Screenshot%202025-09-27%20at%2012.07.46%20PM.png)
## Advantages
+ **Improved query performance** since, the database only needs to search the relevant partition, reducing the amount of data scanned. 
+ **Easier to maintain** since, we can do targeted operations like rebuilding index for a particular partition or collective operations (take less time) like parallelized backups.
+ **Fault tolerance**: Even if one partition fails, others are unaffected.
+ **Enhanced scalability (with [sharding](Database%20Sharding.md)):** When partitioning is implemented across multiple servers (a technique called **sharding**), it allows the database to scale horizontally by adding more machines to handle more data and traffic.
## Disadvantages
- **Limited by a single server:** All partitions are on one machine, so you are still constrained by that server's processing, memory, and storage capacity.
- Database server is a single point of failure.
-  **Increased complexity:** Managing a partitioned table is more complex than managing a single table.
- **Cross partition queries** that need to access data across multiple partitions (such as a join or aggregation) are more complex and slower.
# Horizontal Partitioning
+ Each partition contains subset of rows and is identified by a **partition key**.
	+ In example below, partition key is user_id
![Horizontal-Partitioning](https://media.geeksforgeeks.org/wp-content/uploads/20240510164448/Horizontal-Partitioning.webp)
## When to use
+ The table is very large with many rows.
	+ Partitioning by date range (by month/year) for _time series data_ can significantly improve performance.
+ Separating data by logical groups, such as year or status, makes it easier to manage and archive old data.
	+ You can drop a partition of old data rather than running a complex `DELETE` query on a massive table.
## Considerations
+ A poorly chosen partition key can result in an **uneven distribution** of data or workload, creating performance _hotspots_ on one or more partitions while others are underutilized.
# Partitioning Schemes
## Key-based
Same as [](Database%20Sharding.md#Key%20Based%20Sharding|Key-based%20sharding) but on a single server
## Range-based
Same as [](Database%20Sharding.md#Range%20Based%20Sharding|Range%20based%20sharding) but on a single server
## Round robin
+ Data is cyclically and equally distributed among partitions
![Round-robin-Partitioning](https://media.geeksforgeeks.org/wp-content/uploads/20240515130518/Round-robin-Partitioning.webp)
## Advantages
+ **Random, even distribution** across partitions.
+ Easy to implement.
## Disadvantages
+ Inefficient for range queries.
# Vertical Partitioning
+ Each partition contains a subset of columns of a table. (primary key always included)
	+ Rows are identified by the primary key of table.
![Screenshot 2025-09-27 at 12.37.13 PM](Assets/Screenshot%202025-09-27%20at%2012.37.13%20PM.png)
## When to use
+ The table has a large number of columns.
	+ If only a subset of columns are frequently queried, can separate them out.
+ Separating different data types, like isolating bulky data (images, JSON, or large text) from frequently queried data, making the more frequently accessed tables smaller and faster.
+ Placing sensitive columns, such as financial or personally identifiable information, in a separate partition allows you to apply more stringent security controls and access policies to only that specific data set.
