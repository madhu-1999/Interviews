#computer_networks 
# Overview
+ Hierarchical distributed naming system that translates domain names to IP addresses.
+ Without DNS, we’d have to remember the numerical IP address of every website we want to visit
# Structure
## Root DNS servers
+ Top of the hierarchy.
+ Maintains list of TLD servers
+ Managed by  ICANN
## TLD (Top Level Domain) servers
+ Manages all domains that share a common extension (Ex `.com`)
+ Managed by IANA (Branch of ICANN)
## Authoritative Name servers
+ For a website `example.com` , SLD is `example` and `.com` is TLD.
+ Managed by the Domain Registrar.
+ Contains all information related to a domain Ex: `example.com` 
+ Primary authoritative server maintains R/W copy of zone file and several secondary authoritative servers maintains read only copies. 
	+ Done for load balancing and redundancy

![](https://substackcdn.com/image/fetch/$s_!P_Ol!,w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Ff0a1bb2c-a1bc-40ce-abde-6fb9d2a66ce8_1600x570.png)
# DNS resolution
1. User types domain name into the browser. To resolve the domain name a A or AAAA DNS record is needed.
2. If the device cache has the IP address, domain name resolution finishes here.
3. If not, the local DNS server managed by the ISP provider is contacted. It keeps DNS records of domain names users have accessed.
4. If local DNS server has the record, process is over. It acts as a DNS resolver, contacting various servers in the hierarchy to resolve the requested domain name.
5. If not, the root server is contacted. It provides the IP address of TLD name server
6. The TLD server then, provides IP address of the authoritative name server that holds the required DNS record.
7. The name server then provides the A or AAAA record.
8. This record is then saved in local DNS server and device cache with a TTL.
9. User can now access the website.
There are two types of DNS resolution approaches:
## Iterative
+ DNS resolver actively sends queries down the hierarchy chain to resolve domain name.

![](https://substackcdn.com/image/fetch/$s_!eaJb!,w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F96fbf5fc-bf5d-4047-b058-f48f6f3b3a30_1600x1432.png)
## Recursive
+ The DNS server receiving the query sends query to next server in the hierarchy if it does not have the DNS record.
![](https://substackcdn.com/image/fetch/$s_!ZguZ!,w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F23550856-e23b-4372-8422-20630dfea888_1600x1432.png)
# Types of DNS records
# A record
+ **A** stands for address
+ Shows IP address for a specific hostname or domain.
+ Supports only **IPv4** addresses.
## AAAA record
+ Shows IP address for a specific hostname or domain.
+ Supports only **IPv6** addresses.
## CNAME record
+ Stands for **canonical name**
+ Create a alias for a domain name. Most commonly used to point a subdomain to another domain.  Ex: `www.example.com` points to `example.com`
+ **Cannot be created for a root domain** (ex: `example.com`)
+ **Cannot coexist with other records having same hostname**. Ex: there cannot be CNAME record and a MX record for `www.example.com`.
## NS record
+ Stands for **nameserver**
+ Specifies authoritative DNS server for a domain.
+ Root server has NS records of TLD servers.
## MX record
+ Stands for **mail exchange**
+ Shows which mail server, emails for a domain should be routed to
+ Can have multiple MX records for a domain name.

|Name|Type|​Priority|RDATA|
|---|---|---|---|
|@|MX|10|mx.zoho.com|
|@|MX|20|mx2.zoho.com|
Here, the one with priority 10 is primary mail server and the secondary one is used if primary is unavailable.
## SOA record
+ Stands for **Start of authority**
+ Stores admin information about a domain.
## PTR record
+ Provides domain name for reverse lookup i.e. finding domain name given IP address.
# DNS Zones
+ A zone is a specific portion of DNS that is managed by a single authority.
+  It is like a section of a big map, where each section is controlled separately to make management easier.
+ Zones aren't necessarily physically separated from each other. They are strictly used for delegating control.
+ A single domain can be split into multiple zones.
![UntitledDiagram](https://media.geeksforgeeks.org/wp-content/uploads/20250315103217075806/UntitledDiagram.png)
## Zone types
1. **Primary Zone**: Master copy, and contains all records for the domain it manages.
2. **Secondary zone**: Read only copy of primary zone file used for redundancy.
3. **Stub zone**: Contains only necessary records(SOA, NS, A) to identify authoritative name servers for a zone, but not all records.
## Zone file
+ Config file that stores information about a zone.
+ Contains all DNS records needed to manage that specific zone.
+  one file for a parent domain will **not** contain all the records for a subdomain that has been delegated to its own separate zone.
+ Mandatory records:
	+ SOA record: Contains important admin info about the zone such as primary name server, admin email etc
	+ NS record: Specifies all other servers responsible for the zone. Used to increase reliability and for load balancing purposes.
+ Optional records
	+ A/AAA
	+ MX
	+ CNAME
	+ PTR
+ Directives: Special instructions
	+ $TTL: Sets TTL of local cache for zone.
	+ $ORIGIN: Defines **base domain name** used in file, to avoid repetition and make DNS entries shorter.
# Domain Registration Terminology
1. Domain registrar: Company that is accredited by ICANN to process domain registrations for specific top level domains.
2. Domain Registry: Company that owns right to sell domains with specific top-level domains.
3. Domain Reseller: Company that sells domain names for registrars. Ex: Amazon Route 53 is a domain reseller for Amazon Registrar