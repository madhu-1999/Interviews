#computer_networks 
+ Way devices are connected in a network.
# Point to Point
+ Simplest communication between two nodes.
+ one is the receiver, other is sender
+ High Bandwidth
# Mesh
![Mesh-Topology](https://media.geeksforgeeks.org/wp-content/uploads/20241028174648312121/Mesh-Topology-660.png)
+ Every device connected to every other device through dedicated channels called links.
+ Protocols used: AHCP, DHCP
+ Pros: 
	+ Fault-tolerant
	+ Provides security and privacy
+ Cons: 
	+ Maintenance and installation cost is high (due to dedicated cables)
	+ Suitable for small no of devices.
# Star
![Star-Topology](https://media.geeksforgeeks.org/wp-content/uploads/20241028174732747554/Star-Topology-660.png)
+ All devices are connected to a central **hub**, which may be active or passive.
+ Protocols used: CD, CSMA
+ Pros:
	+ If one node fails, others are not affected.
	+ Easy to setup, and cheap.
	+ Fault identification and isolation is easy
+ Cons:
	+ If hub fails, the network fails
# Bus
![Bus Topology](https://media.geeksforgeeks.org/wp-content/uploads/3-55.png)
+ All devices are connected to a central cable which is bi-directional.
+ Protocols used, CSMA, CD
+ Pros:
	+ Easy to setup and cheap
	+ Suitable for small networks
+ Cons: 
	+ If central cable (backbone) fails, network fails
	+ Adding new devices slows down the network
	+ Security is low
# Ring
![Ring Topology](https://media.geeksforgeeks.org/wp-content/uploads/20241028175715256679/Untitled-Diagram-153-1.jpg)
+ Each device is connected to two neighboring devices. It is uni-directional.
+ Repeaters are used throughout the network, to prevent data loss during transmission
+ Protocol used: Token ring passing protocol
+ In dual-ring topology, two cables are used to connect devices, to make data flow bi-directional.
+ Pros:
	+ Cheaper than star to install and expand
	+ Less collisions
	+ High bandwidth
+ Cons:
	+ Less secure
	+ Addition/removal of nodes disturbs the network topology
	+ Failure of a single node, network fails
	+ Troubleshooting is difficult
# Tree
+ Variant of star, sub-network hubs connect to central hub.![Tree-topology](https://media.geeksforgeeks.org/wp-content/uploads/20240614234036/Tree-topology-660.png)
+ Protocols used: DHCP,SAC
+ Pros: 
	+ Addition of new devices is easy
	+ Error detection and correction is easy
+ Cons:
	+ If central hub fails, network fails.
	+ Expensive to setup, due to cabling cost.
# Hybrid
+ Mix of two or more of above topologies.
+ Pros: 
	+ Flexible
+ Cons:
	+ Challenging to design