#database #nosql #distributed 

# Efficient Storage of Sparse Data
A **Wide Column / Column Family Database** is a type of NoSQL database that stores data in a **schema-less format**, organizing it into column families. Instead of predefined tables, **data is grouped into column families, where each row can have a different number and type of columns**.[^2]
- [p] In an RDBMS, empty columns still occupy storage space or require index overhead. In a column family store, **if a row does not have data for a column, that column is simply not stored at all.**
- [p] Since it is schema-less, we can introduce new columns instantly on a row-by-row basis. 
Following is a representation of how data is stored in a column family database:
```
Column Family: Personal Details  
001 → {Name: "Alice", Email: "alice@xyz.com"}  
002 → {Name: "Bob"}  
  
Column Family: Purchase History  
001 → {Last Purchase: "2024-02-01", Order Count: "5"}
```
# Horizontal Scaling
We need column family stores ==because traditional relational databases (RDBMS) fail to scale efficiently when data reaches massive volumes==.[^1]

To horizontally scale a traditional RDBMS like PostgreSQL or MySQL, we typically use two methods, both of which add massive complexity:
1. [[Database Replication Strategies#Single Leader|Master slave Replication]]: You route all "write" queries to a single master node, which copies data to multiple slave nodes used strictly for "reads".
>[!failure] This only scales **reads**, not writes. If your application suffers from a high volume of incoming writes, the master node still becomes a bottleneck

2. [[Database Sharding]]: You split your tables into smaller pieces (shards) and distribute them across completely separate database servers.
- [c] Traditional RDBMS engines do not natively manage sharding. The application layer or a complex [[Proxy server|proxy]] middleware must handle routing the queries.
- [c] In a single-server RDBMS, joining multiple tables is fast. If you shard your RDBMS across 10 servers, running a `JOIN` query means pulling data over a slow network connection from multiple distinct machines, causing severe latency spikes.
- [c] An RDBMS guarantees that if you update two rows, both updates succeed or both fail (ACID compliance). Doing this across a network requires complex consensus protocols (like Two-Phase Commit). The database must lock rows and wait for all servers to agree, which drastically slows down write performance.

>[!faq] How do wide column stores overcome these disadvantages?

- [p] No support for relational joins and multi-row [[ACID]] transactions.
- [p] They utilize [[Database Replication Strategies#Leaderless|Leaderless]] architecture to offer strong consistency and availability.
- [p] Since rows are self-contained (no join support), the database engine **can automatically [[Database Partitioning|partition]], balance, and route data across thousands of commodity servers seamlessly without application intervention**.

>[!faq] How do wide column stores automatically partition, balance and route data?

They use [[Consistent Hashing]] to automatically **partition** and **balance** data.
Every node in the cluster holds a map of the entire hash ring, thus **any node can accept any read or write request**.
- If a client application sends a write request for `user_id: "john_doe"` to Node 1, but the hash of "john_doe" points to Node 2, Node 1 does not reject it.
- Node 1 acts as a [[Database Replication Strategies#Node coordinator|coordinator]], automatically routing the data over the internal network directly to Node 2. The client application never has to manually calculate which server to talk to.
# High Write Throughput
They use append-only storage structures (like Log-Structured Merge-trees). This turns random disk writes (B-tree indexes) into fast sequential writes, allowing millions of concurrent inputs per second. #cite lsm trees when created.
>[!faq]- How is B-tree index random I/O?
>When you update a user's phone number, the database must look up the exact leaf page where that user lives, navigate to that specific spot on the disk, and overwrite those bytes **in-place**.
>If you write 1,000 updates to 1,000 different users simultaneously, the disk head must constantly bounce around to 1,000 completely different physical locations on the drive. This "random I/O" creates a severe hardware bottleneck.
# Faster Reads
An RDBMS is optimized to save storage space ([[Database Normalization|normalization]]). A column family database is optimized to speed up reads by wasting storage space (denormalization).

Lets look at how RDBMS reads are slower:
- [c] When you run a complex query in an RDBMS that requires data from multiple tables, the database must perform **joins**. If the database is sharded, this requires fetching data across different physical servers over the network, which kills performance.
- [c] If a table has 50 columns and you only query `SELECT age FROM users`, the database engine must still scan and load the entire physical row (including large text columns like `address` or `bio`) into memory before filtering out what you didn’t ask for. 

>[!faq] How do wide column stores overcome these disadvantages?

- [p] No support for joins, thus rows are self-contained i.e. If you need to show an order history with user details, you store the user details _inside_ the order row. The database executes a single lookup on one machine, retrieving the entire dataset from a single continuous block on the disk.
- [p] **Column families are stored together on disk**. If we query only purchase history, the system skips the personal details entirely, leading to faster queries. 
- [p] They utilize a highly optimized read pipeline built on **SSTables (Sorted String Tables)** and in-memory metadata helpers to ensure reads bypass slow disk searches entirely. #cite lsm trees when created.
# Vs Column Stores[^3]

| Feature                                    | Column Family                                                            | [[Row wise vs Column vs Hybrid Data Storage formats\|Column Store]]       |
| ------------------------------------------ | ------------------------------------------------------------------------ | ------------------------------------------------------------------------- |
| [[OLTP vs OLAP vs HTAP\|Primary Use Case]] | OLTP                                                                     | OLAP                                                                      |
| **Operations Focus**                       | High-speed, high-volume concurrent **reads and writes on single rows**.  | High-speed **aggregations, math, and filtering** across whole columns.    |
| **How Data is Stored**                     | Rows are isolated. Column families for a single row are stored together. | Columns are isolated. Every single value for Column A is stored together. |
| **Write Mechanism**                        | Append-only LSM-Trees. Optimized for rapid, continuous writes.           | High overhead for single writes. Data is heavily compressed in batches.   |
| **Examples**                               | Apache Cassandra, ScyllaDB, Google Cloud Bigtable.                       | Snowflake, ClickHouse, Amazon Redshift, Google BigQuery.                  |
# Use cases

# Footnotes
[^1]: [RDBMS and NoSQL Database Scaling](https://ismailgok.medium.com/rdbms-nosql-database-scaling-548716c2bb03)

[^2]: [How do column family databases work?](https://medium.com/@n.joudeh/column-family-databases-a-deep-dive-into-how-they-work-80d4c3e6779e)

[^3]: [Column Family vs Column Store](https://medium.com/@remisharoon/columnar-analytics-vs-column-family-wide-column-databases-00631ce19fb5)
