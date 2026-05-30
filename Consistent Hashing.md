#system-design #distributed 
+ A method for **evenly** allocating keys (data / requests ) among a group of nodes (servers) is called consistent hashing.
# What is hashing?
+ Involves using a hash function $h(x)$ to produce a pseudo-random number i.e. always gives same number. This pseudo-random number is then divided by the number of buckets / servers / size of memory space, to always map it to the same bucket / server / memory space.![Hashing-22](https://media.geeksforgeeks.org/wp-content/uploads/20231212170331/Hashing-22.jpg)
# Need for Consistent Hashing
+ When we add or remove a bucket / server / memory space, the data has to be rehashed so that it always maps to same bucket / server / memory space. This means most of the data has to move to a different bucket after rehashing.
+ Data is distributed unevenly among servers using traditional hashing.
+ When a server fails in a traditional hashing setup, all the data on that server becomes inaccessible until the server is back up or the data is redistributed. There is no good way to handle node failures.
# Consistent Hashing
+ Nodes (servers) and keys (data / request / user) are represented as being part of a **hashring**.![Consistent-Hashing](https://media.geeksforgeeks.org/wp-content/uploads/20231212172745/Consistent-Hashing.jpg)
+ The hashring has infinite points i.e. no fixed size. Position of nodes and keys is defined by the hash function $h(x)$, which must be deterministic i.e. a different value is produced for each key.
+ Keys are assigned to the nodes closest to them in clockwise direction.![Mapping-in-the-hashing-(1)](https://media.geeksforgeeks.org/wp-content/uploads/20231214182509/Mapping-in-the-hashing-(1).jpg)
+ This setup ensures that on addition/removal of nodes, only those keys close to the new node/ removed node need to be rehashed.
+ If a node fails, keys associated with it can be rehashed to other nodes in the network to maintain availability.
# Virtual Nodes in Consistent Hashing
+ In above described consistent hashing, there is ***NO*** guarantee of even data distribution. Addition/ deletion of nodes can skew distribution. No guarantee that nodes on the hashring are divided in a uniform manner w.r.t keys.
+ Above described consistent hashing, is not fault tolerant i.e. keys are lost/ unserviced if node fails.
+ Addition of virtual nodes i.e. virtual replicas of nodes on. the hashring aim to solve these problems.
+ Instead of mapping keys to a single node on the hashring, each node is represented by multiple virtual nodes on the ring. Think of it as partitioning the node.
+ In the example we consider two nodes $s_0$ and $s_1$ . Each node is represented by 3 virtual nodes (Ex: $s_0$ represented by $s_{0\_1}$ $s_{0\_2}$ and $s_{0\_3}$ )![](https://miro.medium.com/v2/resize:fit:1004/1*oYcXIdShxU0i_bSz_PfIJA.png)
+ Keys are assigned to the virtual node closest to them in clockwise direction.
+ As no. of virtual nodes increases, key distribution becomes more even.
+ Fault tolerance can be built by copying keys of one virtual node to other virtual nodes (belonging to same node). Thus, if one virtual node goes down, others still have the keys.

# Applications
+ Data partitoning -> Amazon DyanmoDB, Cassandra
+ CDN -> distribute static content evenly
+ Load balancers -> distribute requests evenly to web/cache servers.