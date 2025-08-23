#computer_networks #ip

A IP address is a **32 bit unique address** for a host that is used when communicating with any device outside the LAN.

# IPv4 address
+ 32 bit address represented by 4 numbers separated by period: 192.168.1.1
+ Divided into two parts: Network ID (first 8 bits) and Host ID (24 bits).
+ There are 2$^8$ networks and 2$^{24}$ hosts per network.  
# Addressing Schemes
## Classful Addressing
+ Obsolete addressing scheme for allocating IP addresses.
+ Categorizes IP addresses into A,B,C,D,E categories.![Classful Addressing](https://media.geeksforgeeks.org/wp-content/cdn-uploads/IP_addressing_3.jpg)
### **Class A**
+ Network Id (8 bits), host id(16 bits)
+ Large networks
+ Default subnet mask : 255.x.x.x
+ First bit always set to 0.
### **Class B**
+ Network id(16 bits), host id(16 bits)
+ Medium sized networks
+ Default subnet mask: 255.255.x.x
+ Higher order bits set to 10.
### **Class C**
+ Network id(24 bits), host id(8 bits)
+ Small networks
+ Default subnet mask: 255.255.255.x
+ Higher order bits set to 110.
### **Class D**
+ Reserved for multicasting.
+ Higher order bits set to 1110.
+ Range: 224.0.0.0 – 239.255.255.255.
### **Class E**
+ Reserved for experimental purposes.
+ Range: 240.0.0.0 – 255.255.255.255.
+ Higher order bits set to 1111
### **Special IP addresses**
+ ***169.254.0.0 – 169.254.0.16*** : Link-local addresses  
+ ***127.0.0.0 – 127.255.255.255*** : Loop-back addresses  
+ ***0.0.0.0 – 0.0.0.8***: used to communicate within the current network.

## CIDR (Classless Inter-Domain Routing)
+ IP address allocation based on prefix. 
+ Allows for more flexible IP addressing and better routing.
+  Representation:  200.10.50.100/20  where 20 is no of bits in network id.
+ Addresses should be contiguous and no of addresses must be a power of 2.
+ First address of block must be evenly divisible with size of block.
+ Mask can be derived from /n suffix bits. Mask will have n 1 bits and rest 0.