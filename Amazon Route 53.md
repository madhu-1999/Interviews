#cloud #aws 
# Prerequisite
[[Domain Name System (DNS)]]
# Overview
+ Highly available (100%) and scalable DNS service.
+ Fully managed and **global** service
+ Performs 3 main functions:
	+ Registering domain names (Domain reseller)
	+ Domain Name Resolution
		+ Route 53 servers are authoritative name servers
	+ Health check of resources.
+ For all record types except **CNAME**, you can enter more than one value
# Hosted Zone
+ A container for records containing information about how you want to route traffic for a domain (example.com) and all of its subdomains (www.example.com, retail.example.com)
+ 4 name servers per hosted zone, called a **delegation set**.
	+ Can create a **reusable** delegation set programmatically and assign to multiple hosted zones (also created programmatically.)
+ Each record contains:
	+ Domain/subdomain name 
	+ Record Type ex: A,AAAA
	+ Value
	+ Routing policy - how Route 53 responds to queries
	+ TTL - time to cache record at DNS resolver
		+ Record that Route 53 responds with has TTL configured, so DNS resolver only caches it for TTL amount of time.
## TTL considerations
- **High TTL (e.g., 24 hours):**
    - Results in fewer DNS queries to Route 53, reducing traffic.
    - Clients cache records for a long time, which may cause them to use outdated records if changes occur.
    - If a record changes, clients may need to wait up to 24 hours to receive the updated record.
- **Low TTL (e.g., 60 seconds):**
    - Increases DNS query traffic, leading to higher costs since Route 53 charges per request.
    - Records are outdated for a shorter time, allowing faster propagation of changes.
    - Easier and quicker to update records overall.
- If you plan to change a record, a common strategy is:
	1. Decrease the TTL to a low value (e.g., 24 hours before the change).
	2. Wait until clients have the new low TTL cached.
	3. Change the record value, which will then propagate quickly.
	4. Increase the TTL again to reduce DNS query traffic.
Note that TTL is mandatory for every DNS record except Alias records
## Public Hosted Zone
+ Contains records that specify how to route traffic on Internet. (public domain names)
+ Automatically creates a SOA and NS record.
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_19728936.png]]
## Private Hosted Zone
+ Contains records that specify how to route traffic within one or more [[Amazon VPC|VPC's]]. (private domain names).
+ Only resolves DNS queries that originate from within those associated VPCs.
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_19728936 (2).png]]
## Public vs Private
| Aspect            | Public Hosted Zone (for public domains)                                                                                                       | Private Hosted Zone (for private domains)                                                                                                                                            |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Accessibility** | Resolvable by anyone on the public internet. This is how normal websites and applications are accessed globally.                              | Resolvable only by clients within a specific, internal network, such as an Amazon Virtual Private Cloud (VPC).                                                                       |
| **Purpose**       | To define how internet traffic should be routed to your public-facing resources, like a website or an application's public IP address.        | To manage DNS for internal network resources that should not be publicly accessible, like a private database or an internal application.                                             |
| **Use case**      | Mapping `www.example.com` to the public IP address of a web server or an application load balancer.                                           | Mapping `database.internal.example.com` to the private IP address of a database server within a VPC.                                                                                 |
| **Security**      | Security is managed at the network level (e.g., firewalls) and by DNS extensions like DNSSEC, but the domain's existence is public knowledge. | Enhanced security is provided by obscuring internal network details from the public internet entirely. The DNS records are simply not discoverable from outside the private network. |
| **Naming**        | Typically uses standard top-level domains (TLDs) like `.com`, `.net`, and `.org`.                                                             | Can use any naming scheme, including real domain names or special TLDs like `.internal`, as long as they are managed internally.                                                     |
# Alias Record
+ Alias record is a Route 53 specific feature, used to point an alias to a AWS resource.
	+ `myapp.mydomain.com` points to AWS hostname `lb1-1234.us-east-2.elb.amazonaws.com`
+ Can be used for both root domain and subdomain.
+ Resolves directly to an IP, acting like a A or AAAA record.
	+ Auto updates changes in IP address of target AWS resource.
+ Can coexist with other record types for same hostname. Ex: there can be a Alias record and a MX record for `example.com`.
+ Performs health check for target AWS resource.
## Supported AWS targets
+ [[Amazon ELB|Elastic Load Balancers]]
+ CloudFront Distributions
+ API Gateway
+ Elastic Beanstalk environments
+ S3 Websites
+ VPC Interface Endpoints
+ Global Accelerator accelerator
+ Route 53 record in same hosted zone
## CNAME vs Alias
| Feature           | CNAME Record                                                        | Alias Record                                                                                       |
| ----------------- | ------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| **Target**        | Any hostname anywhere.                                              | Only specific AWS resources (e.g., ALB, S3, CloudFront) or another record in the same hosted zone. |
| **Zone Apex**     | Not supported for the root domain (e.g., `example.com`).            | Supported for the root domain.                                                                     |
| **Performance**   | Slower, as it requires at least two DNS lookups.                    | Faster, as Route 53 returns the IP address directly, like an A record.                             |
| **Other Records** | Cannot coexist with any other record types for the same hostname.   | Can coexist with other record types (e.g., MX) for the same hostname.                              |
| **Cost**          | Standard Route 53 query charges apply.                              | Free for queries that are routed to AWS resources.                                                 |
| **TTL**           | Can set a custom Time-to-Live.                                      | TTL is automatically managed by Route 53 and cannot be set.                                        |
| **IP Updates**    | Does not automatically update when the target's IP address changes. | Automatically recognizes and updates to reflect IP address changes of the target AWS resource.     |
**Use a CNAME record when:**
- You need to point a subdomain to a hostname outside of AWS. Ex: `blog.example.com` to a third-party blogging platform.
- You are aliasing one subdomain to another. Ex: `www.example.com` to `example.com`.
- You need to point to a service that is not on the list of supported AWS Alias targets.
# Routing Policies
+ Determines how Route 53 responds to queries
## Simple Routing
+ Typically route traffic to a single resource.
+ Cannot create multiple records with same name and type but can specify multiple values in one record.
	+ If multiple values, client will pick any one at random.
+ If Alias record, can specify only one AWS resource.
+ **Can't** be associated with health checks.
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_19728958.png]]
## Weighted Routing
+ Can associate multiple resources with same domain/subdomain.
	+ Create multiple records with same name and type. (one per resource)
+ Control how much traffic (%) is routed to each resource. i.e % of a specific IP address  being returned to resolver.
$$\text{traffic \%} = \frac{\text{Weight of record}}{\text{Sum of weights of all records}}$$
+ Can be associated with health checks.
+ Assign $weight\space = 0$ to a record to stop sending traffic to a resource.
	+ If all records have $weight\space=0$ , then all records returned equally.
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_19728960.png]]
## Latency based routing
+ Route to a resource in the [[AWS Global Infrastructure#Region|AWS region]] with lowest latency.
	+ Multiple latency records (one per resource) are created for multiple AWS Regions.
	+ On receiving a DNS query, Route 53 determines: 
		+ which AWS region there are records for
		+ which AWS region among them has lowest latency
		+ which resource (specified in record) in selected AWS region to route traffic to.
	+ Returns IP address of selected resource.
	+ Latency data between users and resources is based entirely on traffic between users DNS resolver and AWS data centers.
		+ It **does not** measure total time taken from user device to destination (which can be outside AWS)
		+ Note that the calculation for latency of region is more complex.
+ Can be associated with health checks.
## Failover routing
Read [[#Automated DNS failover]] before proceeding.
+ [[#Active Passive]] failover configuration
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_19728974 1.png]]
## Geolocation routing
+ Route to a resource based on geographic location.
	+ Can specify location by continent, country or US state.
	+ Ex: Route all traffic from Europe to a ELB in Frankfurt.
	+ If two records have overlapping regions, the smaller geographic region is prioritized. 
		+ Ex: Records for Asia and India. If user is in India, routed to India resource.
+ IP address provides the user's approximate location, since it is tied to a particular region by ISP.
	+ Some IP addresses are not mapped to a location.
+ If query comes from a location for which there is no geolocation record, "no answer" response is returned.
+ Can create a "default" record, which will be returned if query comes from unknown location or from a location with no geolocation record. (Set location to default)
+ Use cases:
	+ Restrict distribution of content to certain locations.
	+ Localize content, ex: present website in local language
	+ Load balancing, such that a user location always routes to same resource.
## Geoproximity routing
+ Route user to resource closest to it geographically.
+ Can optionally, choose to send more/less traffic to a specific resource by increasing/decreasing _bias_.
	+ It expands/shrinks the geographic region from which traffic is routed to the resource.
	+ To expand, specify a positive integer between 1 and 99 for bias.
	+ To shrink, specify a negative integer between -1 and -99 for bias.
	+ When the geographic region of a resource is expanded, then region of adjacent resources shrinks and vice versa.
	+ $\text{Biased distance} = \text{actual distance} * [1 - (bias/100)]$ 
+ Need to specify
	+ AWS region / [[AWS Global Infrastructure#Local Zones|Local Zone]] for AWS resources.
	+ Latitude and Longitude for non-AWS resources.
No bias:
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_26101622.png]]With +ve bias: ![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_26101622 (1).png]]

## IP based routing
+ Based on **client's IP address**
+ Need to provide a list of CIDR's and corresponding endpoints/locations.
+ Use cases: 
	+ Route users from a ISP to a specific endpoint. 
	+ Optimize performance
	+ Reduce network cost
+ Cannot be used for private hosted zones.
## Multi value routing
+ Lets you return multiple values (ex: IP addresses) in response to DNS queries
	+ Only returns values of healthy resources.
	+ One record per resource
	+ Can return up to 8 values
+ Can be associated with health checks.
	+ If no health check attached to a record, always healthy
	+ If all resources are unhealthy, returns all values.
## Traffic flow policy
+ Simplifies creation and maintenance of records in [[#**Complex configuration**|complex configurations]].
+ Visual editor to manage complex routing decision trees.
+ Can create multiple versions of the same traffic policy (unchanged configs carry over).
+ Root record represents all records created as part of decision tree. i.e. only root record shows up on list of records of hosted zone. Need to specify:
	+ hosted zone
	+ name of record at root of tree (ex: `example.com`)
+ Can create records for multiple hosted zones in same traffic policy.
	+ Ex: Same web server used for multiple domains, like `example.com` , `example.org` and `example.net`.

# Health Checks
+ Monitor health of **public** resources. (AWS and non-AWS resources)
+  A new health check is considered healthy until there us enough data to determine the actual status.
+  A _disabled_ health check is considered _healthy_.
+ If status of health check is _inverted_, then 
	+ a new health check is initially considered unhealthy 
	+ a healthy endpoint unhealthy.
	+ a disabled health check is considered unhealthy.
## Automated DNS failover
+ If multiple resources perform same function,
	+ Ex: multi-AZ application setup but access through same domain name
and  we want traffic to be routed **only** to healthy resources, we can configure automated DNS failover to route traffic to a healthy resource if selected one is unhealthy.
### **Simple configuration**
+ Multiple DNS records with same name, type and routing policy.
+ Can be a mix of non-alias and alias records.
***Alias records***:
+ For AWS resources
+ Specify Yes for Target Health Check while creating record
***Non-Alias records***
+ For non-AWS resources
+ Create health check and then associate it with the record.
### **Complex configuration**
Read more: [Documentation](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-failover-complex-configs.html)
+ Can be a mix of non-alias and alias records.
+ Create a decision tree based on multiple criteria to decide where traffic should be routed.
+ Can be a mix of different routing policies.
![](https://docs.aws.amazon.com/images/Route53/latest/DeveloperGuide/images/hc-latency-alias-weighted.png)
## Types
1. **Endpoint Health Check**: Monitor a public endpoint such as application, AWS resource or server.
2. **Calculated Health Check**: Monitor other health checks and combine their results.
3. **CloudWatch Alarm Health Check**: Monitor CloudWatch alarms, useful to monitor private resources.
## How Endpoint Health Checks Work
### **Creation**
+ Need to specify:
	+ IP address or domain name of public endpoint.
	+ Protocol to use for health check (HTTP, TCP, HTTPS)
	+ *Request interval*: how often to send requests (10s or 30s)
	+ *Failure Threshold*: no of consecutive failures (no response or incorrect response) before deeming it unhealthy.
	+ Notification configuration: If configured, when a endpoint is deemed unhealthy, a CloudWatch alarm is triggered, which uses [[Amazon SNS]] to notify users that the endpoint is unhealthy.
### **Working**
+ AWS health checkers ($\approx 15$) are distributed globally and send requests to the public endpoint configured in health check.
+ If over 18% of health checkers deem a endpoint healthy, it is considered healthy.
	+ If 2xxx or 3xx response received, endpoint is healthy by a health checker.
	+ Can be setup to pass/fail based on text in the first 5120 bytes of the response.

>[!warning]
>+ The endpoint needs to be configured (router/firewall/security group) to accept traffic from IP address ranges of AWS health checkers.
>+ Inbound rule allowing specified protocol (HTTP/HTTPS/TCP) traffic from anywhere / IP address range works if endpoint is a AWS resource.


![Conceptual graphic that shows how you configure Route 53 to monitor the health of specified endpoints.](https://docs.aws.amazon.com/images/Route53/latest/DeveloperGuide/images/how-health-checks-work.png)
## How Calculated Health Checks work
+ The health check doing the monitoring is called _parent health check_ and the ones being monitored are _child health checks_.
+ One health check can monitor up to 256 health checks.
+ The health check is reported healthy if: (below are configurations to choose from)
	+ _X_ of _Y_ **selected** child health checks are healthy
		+ If selected health checks > monitored health checks => unhealthy
		+ If selected health checks = 0 => healthy
	+ All child health checks are healthy
	+ At least one child health check is healthy

>[!note]
>A calculated health check cannot monitor other calculated health checks

## How CloudWatch alarm health checks work
+ Monitors **data stream** ***NOT*** status of the CloudWatch alarm.
+ If data stream indicates that the state of alarm is 
	+ ***OK***, then it is considered healthy.
	+ ***ALARM***, then it is considered unhealthy
+ If there is not enough information to determine status, then it depends on the **Health check status** specified: healthy, unhealthy or last known status.
## Health check for private resources
+ Route 53 health checkers are outside VPC, so they cannot access private endpoints or on-premises resources.
+ Create a CloudWatch metric, associate with alarm and create a health check that checks alarm.
# Failover configurations
## Active Passive
+ Use when a _primary_ resource or group of resources should be available majority of the time and you want _secondary_ resource on standby, in case _primary_ becomes unavailable.
+ [[#Failover routing]] policy is used.
	+ Create one DNS record for _primary_ resource (single value) or group of resources (multiple values).
	+ Create one DNS record for _secondary_ resource (single value) or group of resources (multiple values).
	+ If at least one resource in primary DNS record is healthy, failover does not happen.
## Active Active
+ Use when you want all resources to be available for a majority of the time.
+ When a resource becomes unhealthy, Route 53 stops sending it requests.
+ All records with same name, type and routing policy are considered active, unless health check deems it unhealthy.
##
For more clarity on the concept, see [[Scaling#Failover strategies]] 