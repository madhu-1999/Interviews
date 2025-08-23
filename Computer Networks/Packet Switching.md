#computer_networks 
+ Instead of sending data in one big chunk, it is divided into packets.
+ Each packet contains destination address, sequence number and a portion of the data being transmitted.
+ The packets hop from one router to another. At each hop, best route to reach destination address is determined and packet is sent to next hop.
+ This makes the network flexible, since packets can traverse a different route if some route has heavy traffic or a router fails.

# Types of Packet Switching
## Datagram Switching/ Connectionless Switching
+ The route to be traversed is not decided upon at the start.
+ Packets arrive at destination in any order and are pieced together using sequence numbers.

## Virtual Circuit Switching/ Connection-oriented Switching
+ Route to be traversed is decided upon at the beginning.
+ All packets traverse fixed path in order to reach destination.