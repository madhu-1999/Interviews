#aws #cloud 
# Prerequisite
[[Caching]]
# Overview
+ [[Amazon RDS#Fully managed Services|Fully Managed]] Service to get managed Redis, Memcached or Valkey cache.
	+ AWS takes care of OS maintenaince, patching, optimization, setup, configuration, monitoring, failure recovery and backups.
+ In-memory database with high performance, low latency
+ Helps reduce load off of database for ready heavy workloads.
+ **Involves heavy code changes**
+ In a Multi-AZ setup, AWS automatically provisions a standby instance in a different AZ, replicated asynchronously.
	+ Read replicas (upto 5) can also be provisioned. All the read replicas are managed by a reader endpoint.
+ Cluster Mode
	- Disabled: Single shard with one primary node and up to five read replicas.
	- Enabled: Multiple shards across multiple servers.
- Need to attach subnet groups (in which cache can be used) during creation.
- Can attach security groups for network security.
-  Ideal for 
	- storing session data
	- API responses
	- database query results
	- other information that applications require repeatedly.
- Write through and cache-aside caching strategies available.
- Cache evictions
	- Delete explicitly
	- Memory full (LRU)
	- TTL
# Deployment options
## Serverless
+ Ideal for
	+ Unpredictable workloads
+ Simplifies cache creation
+ Internally auto-scales i.e. no control over scaling parameters.
+ No need to provision or manage cache capacity
+ Redundantly stores data across 3 AZ's asynchronously.
+ Provides encryption at rest and transit.
+ Supports cache level metrics
+ Backups from a self-designed cluster can be restored into serverless config.
## Self designed cluster
+ Ideal if
	+ Want more fine-grained control
	+ Predictable workloads
+ Can design (choose no of nodes, cluster size, node placement across AZ) cache cluster.
+ Can control auto-scaling parameters (not for Memcached).
+ Can choose between single or multi-AZ setup
+ Can enable/disable encryption at rest and transit
+ Supports cache level and node level metrics
+ Allows customer to do minor/major version upgrades on infrastructure.
+ Backups from a serverless cluster can be restored into self-designed config.
# Redis vs Memcached
## Redis
- Supports multi-availability zones with auto-failover.
- Allows creation of read replicas to scale reads and ensure high availability.
- Provides data durability through Append Only File (AOF) persistence.
- Offers backup and restore features in the open-source version.
- Supports sets and sorted sets, useful for features like leaderboards.
## Memcached
+ Uses multiple nodes that partition your data, a process called sharding.
- Does not provide high availability or replication.
- The serverless version offers backup and restore features, but the self-managed version on ElastiCache does not.
- Employs a multi-threaded architecture, which can improve performance.