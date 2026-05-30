#computer_networks 
![OSI-Model-.webp](https://media.geeksforgeeks.org/wp-content/uploads/20250117112545142665/OSI-Model-.webp)

# Physical Layer
![physical-layer-in-OSI](https://media.geeksforgeeks.org/wp-content/uploads/20241015103017414021/physical-layer-768.png)
 + This layer is responsible for transmitting individual bits from one node to the other. Forwards received data to data link layer.
 + It specifices how the devices are arranged in the network (topology).
 + Devices: Hub, Repeater, Modem, Cables
# Data Link Layer
+ Main function is to ensure that data transfer from one node to the next is **error-free**.
+ Unit of encapsulation is a **frame**.
+ ***Error Control:*** The data link layer provides the mechanism of error control in which it detects and retransmits damaged or lost frames.
+ - ***Flow Control:*** The data rate must be constant on both sides else the data may get corrupted thus, flow control coordinates the amount of data that can be sent before receiving an acknowledgment.
+ - ***Access Control:*** When a single communication channel is shared by multiple devices, the MAC sub-layer of the data link layer helps to determine which device has control over the channel at a given time.
+ MAC addressing used on this layer.
+ Devices: Switch, Bridge

# Network Layer
+ Responsible for data transmission between nodes in **different networks**.
+ Handles packet routing i.e. shortest path packet can take to reach destination.
+ Unit of encapsulation: **packet**.
+ IP address used on this layer
+ Devices: Switch, Router

# Transport Layer
+ Responsible for end-to-end delivery of data between nodes.
+ Protocols used: TCP/IP, UDP
+ Sends acknowledgements for successful transmission and retransmits if error has occurred.
+ Unit of encapsulation: **Segment**
+ Adds destination and source port to the header.

# Session Layer
+ Responsible for managing and establishing connections, terminating sessions between two devices.
+ Provides authentication and security.
# Presentation Layer
+ Data from application layer is extracted and manipulated to make it fit for transmission over the network.
+ Handles encryption/decryption and compression.
+ Protocols: JPEG, TLS/SSL, GIF, MPEG
# Application Layer
+ Serves as a window for application services to access the network. Services generate data which needs to be transmitted.
+ Protocols: DNS, SMTP,FTP
