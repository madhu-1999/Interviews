#system-design  #distributed 
+ Distributed network of servers that deliver content (images, video, files) to clients faster and more efficiently.
# Components of CDN
+ ***Edge servers***: Servers distributed across geographic locations, strategically to be close to clients. It caches content received from origin server and delivers it to clients.
+ ***Origin server*** : Primary source of distribution where all the original content is stored.
+ ***Content Distribution Nodes***: Responsible for delivering content through optimized route within the CDN.
# Working of CDN
+ When client requests content, CDN identifies the client's location and checks the edge server closest to it for the content.
+ If the content is not present, CDN fetches it from the origin server and caches it in the edge server before returning response to the client.
+ The next time, the content is requested, the edge server can return it from the cache, improving performance and reducing latency.
![[Screenshot 2025-05-28 at 6.02.08 PM.png]]
+ In a multi-tier setup, instead of directly requesting content from origin server, it requests server above it in the hierarchy of edge servers/ edge server closest to it, recursively till it can fetch the content.
+ Each piece of cached content has a TTL, after which it will be invalidated and refreshed from source.
# Advantages
- ***Faster Content Delivery***: CDNs improve load times and lower latency by reducing the physical distance that data must travel by caching content on servers that are closer to end users.
- ***Improved Website Performance***: Improved website performance, including longer visit durations, higher user engagement, and higher conversion rates, is an immediate result of faster load times.
- ***Scalability***: CDNs help websites handle traffic spikes and high loads by distributing the load across multiple servers. This scalability is especially crucial for websites with global audiences or those experiencing sudden surges in traffic.
- ***Redundancy and Reliability***: CDNs offer redundancy by storing copies of content across multiple servers. If one server fails, another server can seamlessly take over, ensuring continuous availability of the content.
- ***Cost Savings***: By reducing the load on origin servers and optimizing content delivery, CDNs can help lower bandwidth costs and infrastructure expenses for website owners.
- ***Security***: CDNs provide additional security features, such as DDoS protection, SSL/TLS encryption, and web application firewalls, helping to protect websites from various online threats.
# Disadvantages
- ***Cost****: Implementing and maintaining a CDN can incur additional costs compared to relying solely on the origin server.
- ***Complexity****: Managing and optimizing a CDN requires technical expertise and ongoing maintenance.
- ***Security considerations****: Ensuring data security while using a CDN requires careful configuration and adherence to security best practices.