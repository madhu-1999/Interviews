#security 
# Forward Proxy
+ Acts as a gateway between device and internet, masking IP address and enhancing online privacy.
+ Can be used bypass restrictions as well as restrict websites.
![proxy_server](https://media.geeksforgeeks.org/wp-content/uploads/20250804163627434033/proxy_server.webp)
## Working
- You open a website in your browser.
- Instead of going directly to the site, your request first goes to the proxy server.
- The proxy reads your request and sends it to the website on your behalf.
- The website sends the response back to the proxy (not directly to you).
- The proxy checks the response for security issues (like malware).
- If everything looks fine, the proxy sends the data to your browser.
This process hides your actual IP address and can also make your connection more secure or faster in some cases.
# Reverse Proxy
+ Same as forward proxy, except it protects **servers** not clients. i.e. hides IP address of servers.
+ Listens to the request made by the client and redirect to the particular web server. 
+ Can be used to
	- ***Load balancing:*** distribute the load to several web servers.
	- ***Cache static content:*** offload the web servers by caching static content like pictures, HTML pages.
	- ***Compression:*** compress and optimize content to speed up load time.
# Database Proxy
+ Type of reverse proxy.
## Working
+ When your application needs to fetch or store data, it sends a request. Instead of going directly to the database, this request first goes to the database proxy.
+ The proxy then processes the request, possibly optimizes it, and forwards it to the database.
+  Once the database responds, the proxy returns the response to your application.
## Potential Capabilities
### **Performance Enhancement**
+ Can manage multiple database connections through connection pooling.
+ Reuses existing connections instead of creating new ones.
+ Reduces overhead and time establishing connections, making storage and retrieval of data faster.
### **In-built caching**
+ Can store results of frequent queries for instant access.
### **Security**
+ Add an extra layer of security by acting as a gatekeeper between your application and the database.
+ Can enforce security policies, ensuring only authorized requests reach the database.
+ Can help with data masking and encryption
### **Load Balancing**
+ Can distribute incoming requests evenly across all available servers, preventing any one server from being overwhelmed.
+ Can automatically redirect traffic to another server if one fails.
Read [](Database%20Sharding.md#Use%20a%20routing%20layer%20(Recommended)|Database%20proxy%20in%20sharding) for more features related to sharding.