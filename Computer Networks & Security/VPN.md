#security 
# Advantages
+ Can hide IP address on the internet, even from the ISP.
+ VPN encrypts traffic, making it unreadable to ISP.
+ Prevents bandwidth throttling, intentional slowdown of internet connection by ISP.
+ Can bypass geo-restrictions.
# How does it work
+ Initiate connection to a VPN server in a country of your choice.
	+ VPN client on your device authenticates with server.
+ Server and client, create a encrypted connection called a **tunnel**.
	+ All traffic passing through the tunnel is encrypted.
	+ Only the server and your device has the key to decrypt.
+ Any data you send to the internet first passes through the encrypted tunnel, to the VPN server.
	+ Server masks your IP address, decrypts traffic and forwards it to its destination and vice versa.
# Types
## Personal VPN
+ For individual users who want to enhance security and privacy while browsing.
## Remote Access VPN
+ Connects a user to a private network such as company's internal network.
+ Used to securely access company resources from anywhere in the world.
+ VPN client on user's device creates a encypted tunnel with VPN gateway of company's network.
## Site-to-Site VPN
+ Connect two or more networks together such as corporate headquarter to branch office.
+ A VPN gateway is set up at each site to create a permanent, encrypted tunnel between the networks.
## [[SSL & TLS|SSL/TLS]] VPN
+ Establishes secure connection using SSL/TLS through web browser. 
	+ No VPN client required.