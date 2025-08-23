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
# What is caching?
+ Technique to store frequently accessed data in fast access memory over slow access memory.
+ Reduces latency and makes system faster
+ Improves fault tolerance
+ Different types of caching in different layers of the system:
	+ Client side: Browser cache
	+ Server side: Redis
	+ CDN for static data caching
![[Screenshot 2025-04-12 at 3.02.34 PM.png]]
# Cache tiering
+ Multiple layers of cache are used to improve data access performance.
	+ L1 Cache (in-memory cache): Fastest cache, stored in system memory. Holds data needed/used most.
	+ L2 Cache (Out of process cache): Larger than L1 but slower. Stored on separate server or device. Stores data that is needed/used less than the data in L1.
	+ L3 Cache (Distributed Cache): Optional layer, that combines data from multiple sources and improves availability.
+ Data access goes from L1 -> L2 -> L3 -> db
+ Data synchronization:
	+ Write Through: Written to all caches simultaneously.
	+ Write Back: First written to L1, then asynchronously written to L2/L3.
# Why should cache be distributed?
+ Single cache becomes a **single point of failure**. It it goes down, affects app performance.
+ A cache tier can be scaled independently.
+ To lower latency (think geography)
+ Fault Tolerance: If one cache server fails, requests can be rerouted to other cache.
# How is cache distributed ?
+ [[Consistent Hashing]] and [[Consistent Hashing#Virtual Nodes in Consistent Hashing|Virtual Nodes]] are used in distributed caches.
# Caching Strategy
+ All strategies need to use a cache eviction policy to replace older data when cache is full.
+ There has to be some expiry time after which data in cache is **invalidated** to ensure outdated data is not present in it. Cache can be refreshed after expiry time.
## Cache Aside
+ Lazy loading of data, i.e. new data is written to cache only when requested. The Server/Application is responsible for fetching the data.![](https://hazelcast.com/wp-content/uploads/2021/12/cache-aside-read-1.svg)
+ Data is read first from the cache, however writes are to database.
![](https://hazelcast.com/wp-content/uploads/2021/12/cache-aside-write-1.svg)
### **Advantages**
+ Good for read-heavy applications.
+ Request will not fail even if cache is down, since application is responsible for fetching the data.
### **Disadvantages**
+ Data can become inconsistent between cache and db after a update, wiithout proper synchronization.
+ For new data, there will always be cache miss.
## Read Through
+ Lazy loading of data for reads i.e. fetched only when requested. The cache is responsible for fetching the data.
+ Writes are to database.
![](https://hazelcast.com/wp-content/uploads/2021/12/read-through.svg)
### **Advantages**
+ Good for read heavy applications.
+ Separation of concerns since cache updation is not responsibility of application.
### **Disadvantages**
+ Data becomes inconsistent between cache and db after a update.
+ For new data, there will always be cache miss.
## Write Through
+ Write first to cache, then to db (as a **transaction** i.e. synchronous).
![](https://hazelcast.com/wp-content/uploads/2021/12/write-through.svg)
### **Advantages**
+ Cache and db remain consistent.
+ Cache hit chances increase
### **Disadvantages**
+ Has to be used in combination with cache-aside / read-through cache, otherwise it only increases latency (write to cache, write to db).
+ 2 phase commit support needed to maintain transactional property.
+ If either cache or db is down, write fails.
## Write Behind
+ Write first to cache, then to db. Cache sends **asynchronous** write to db. Response to client is returned after cache write.
![](https://hazelcast.com/wp-content/uploads/2021/12/write-behind.svg)
### **Advantages**
+ Good for write heavy applications
+ Write operation latency better than write through since db write is asynchronous.
+ Cache hit chance increases.
+ Gives better performance than write through when b=paired with read-through cache.
### **Disadvantages**
+ Write is successful, even if db write fails or db fails.
+ Inconsistency can occur if db write doesn't happen before cache expires and client wants to access this data.
## Write Around
+ Writes to db only. Entry in cache (if exists) is invalidated.
+ Cache is updated on cache miss.
![How write-around caching strategy works?](https://ucarecdn.com/52e92dc2-d666-4d33-8f87-cdbeecde8842/ "Write-around caching pattern")
### **Advantages**
+ Good for read heavy applications
+ Resolves inconsistency between cache and db.
+ Used in tandem with cache-aside/ read-through.
### **Disadvantages**
+ Cache miss on new data read.
+ If db is down, operation fails.
# Cache eviction policies
+ Policy determines which item in cache needs to be replaced to make room for new data when it is full.
## LRU (Least Recently Used)
+ Replace the item which hasn't been used the longest.
+ The idea is simple: if you haven’t accessed an item in a while, it’s less likely to be accessed again soon.
![](https://miro.medium.com/v2/resize:fit:1068/0*BZVTu1PsINhRky_d.png)
## LFU (Least Frequently used)
+ Replace the item that has the lowest access frequency.
+ It assumes that items less frequently accessed are less likely to be accessed in the future.
+ For ties, a secondary strategy like LRU/ FIFO can be used to break ties.
![](https://miro.medium.com/v2/resize:fit:1088/0*QH4GAen2o3wJFjgz.png)
## FIFO
+ Replaces item that was added first.
+ It assumes that items added earliest are less likely to be accessed.
![](https://miro.medium.com/v2/resize:fit:1306/0*KMnKQezCfH8jlAJt.png)
## RR (Random Replacement)
+ Replaces a random item in the cache.
+ Effective for systems with dynamic access patterns or that are unpredictable ![](https://miro.medium.com/v2/resize:fit:1120/0*E_N3SJiW7BjD1k1V.png)
## MRU (Most Recently Used)
+ Opposite of LRU.
+ Assumes that most recently used item is likely temporary, and won't be accessed soon.
![](https://miro.medium.com/v2/resize:fit:1068/0*WoVdcUcR9ITantop.png)
## TTL (Time to Live)
+ Each item is assigned a expiration time and is removed when the time is up irrespective or frequency/access patterns or other factors.
+ Ensures that no stale data is present in the cache.
![](https://miro.medium.com/v2/resize:fit:1164/0*7eH8DKva4bNG3ylY.png)
# Cache invalidation policies
+ If data present in cache is updated in db, there is inconsistency between cache and db. Cache invalidation aims to make stale data in cache invalid so that client always receives fresh data.
## Programmatic Invalidation
+ Code responsible for db write also handles cache invalidation.
+ Requires careful coordination to ensure all cache instances are updated.
## TTL (Time To Live) Invalidation
+ Each piece of data in cache has a TTL associated with it. When time is up, the data is invalidated and fetched again from the db.
+ Useful for data that doesn't require constant accuracy and can handle being outdated for some time.
## Event Based Invalidation
+ When an event, like update occurs, the system can send a signal/message to cache. On receiving the message/signal, the cache can invalidate the relevant data.
+ Requires messaging / event infrastructure.
## Version Based Invalidation
+ Each piece of data has a version number associated with it. On update, the version number is incremented. When update occurs, db version and cache version differ, then data in cache is invalidated and latest data is fetched.
## Key Invalidation
+ Each cache entry is linked to one or more keys. When data associated with a key changes, cache is invalidated for those keys.
## Layered Invalidation
+ In a multi-cache setup, on update only the cache containing updated data is invalidated.
## Lazy Invalidation
+ On update, cache entry is marked invalid (but not removed). When a read request for this data comes in, new data is fetched into the cache.