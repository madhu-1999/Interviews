#security #computer_networks 
# Overview
+ **SSL/TLS** (Secure Sockets Layer / Transport Layer Security) is a protocol that provides secure, encrypted communication between two computers over the internet, such as a web browser and a server.
+ SSL protocol has been replaced by the more secure TLS protocol, but the term "SSL" is still widely used.
+ **Certificates**, or digital certificates, are files that enable SSL/TLS encryption.It performs two main functions: 
	- **Authentication**: It verifies that the server you are connecting to is legitimate and not an impostor.
	- **Encryption**: It provides the public key needed to establish an encrypted connection.
# How they work
 When you connect to a secure website (starting with `https://`): 
1. Your browser requests a secure connection to a website.
2. The website's server sends a copy of its SSL/TLS certificate to your browser.
3. Your browser checks the certificate to ensure it is valid and issued by a trusted Certificate Authority (CA).
4. If the certificate is valid, your browser and the server use the certificate's public key to generate a unique, temporary "session key".
5. All subsequent data exchanged during that session is encrypted using this session key, keeping your information private and secure.
# Server Name Indication (SNI)
+ Extension to the TLS protocol that allows a web server to host **multiple** websites with **different** SSL/TLS certificates on the **same IP address**.
## Need for SNI
+ Before SNI was created, a single IP address could only host one website with its own unique SSL/TLS certificate, leading to a IPv4 address shortage.
	+ This was because the TLS handshake, the initial step in creating a secure connection, happens before the client's browser sends an HTTP header specifying which website it wants.
	+ Which means, if multiple websites are hosted on same IP,  impossible to know which websites's TLS certificate to present for the handshake.
+ When SNI is used, the hostname of the server is included in the TLS handshake.
## How it works
1. **Client Hello with SNI**: Your browser sends a "Client Hello" message to a server's IP address. This message includes the SNI extension, which specifies the hostname, such as `www.example.com`.
2. **Server responds with correct certificate**: The server receives the request, reads the hostname from the SNI field, and then sends back the correct SSL/TLS certificate for `www.example.com`.
3. **Secure connection established**: Your browser and the server can now complete the TLS handshake and establish a secure, encrypted connection to the correct website.
