# Principles of Network Applications
Communication between applications running on end systems takes place at the application layer. Network applications on any type of end systems - laptop, desktop, server, iOS, Android, etc - can communicate with each other because their communication is separated from the transportation of the message.

## Network Application Architectures
In **client-server architectures** there is an always-on host with a fixed IP address called a server. This host is communicated with by one or more client hosts, for example a web browser on someones laptop. Because the server is always on, the users laptop is always able to find the server when it needs to communicate with it. Since a single server can easily be overwhelmed by the amount of traffic a busy website can generate, many servers are gather together inside a **datacentre**.

**Peer to Peer architectures**, on the other hand, do not rely on a single host with which to communicate, rather they communicate with other end systems running the same network application, usually laptops and desktop controlled by users. P2P architectures have the advantages of scalability, since each peer not only generates workloads for other peers in the network, but provides capacity by serving resources. The drawback to this decentralised model is performance (the network relies on the users internet connection, the quality of which will vary), reiability (the peers in the network can come and go, causing capacity and availability of shared resources to flux) and security (any peer can join the network with no credibility).

## Processes Communicating
Processes communicate across a network by sending **messages** to one another in pairs - a client and a server. On the web, a browser accessing a server is called the **client** and the **server** is called the server. In P2P applications, the host that is downloading a file is called the client, the one hosting it is called the server. In a P2P network, a single end system can be both a client and a server simultaneously. Generally, the process initiating the communication is called the client, and the process waiting to be contacted is called the server.

![[Screenshot 2021-07-05 at 17.52.11.png]]

Messages enter and leave an end system through an interface called a **socket**. The network application pushes its message into the socket, assuming trapsorting infrastructure will be on the other side to pick it up. A socket might offer a choice of transport protocol, as well as allow the application developer to set some protocol-specific parameters, but the transportation logic is hidden away behind the socket **API**.

In order for a message to reach its destination, it must be transmitted with two pieces of information: an **IP address** and a **port number**.

> A **socket** is the interface between an application process and the transport layer protocol.

## Transport Services Available to Applications
Choosing a transport layer protocol is like choosing a mode of transport between cities, eg plane or train. Each offers different services which can be broadly categorised as:
- **Data Transfer** - some applications cannot tolerate loss during transit, such as email and banking data, whereas multimedia applications such as video and voice call can. When a transport protocol provides reliable data transfer, the application can send its data into the socket completely confident that it will all reach its destination.
- **Throughput** - if a transport layer protocol is able to provide thorughput guarantees, then **bandwidth-sensitive applications**, such as internet telephony, can request a specific throughput when they send their data into a socket, while **elastic applications**, such as email, can use as much or as little as is available.
- **Timing** - this is where a protocol guarantees data is received at the destination no more than x milliseconds after it was transmitted.
- **Security** - this is where a transport layer protocol encrypts the data after it enters  the sending socket and decrypts it after it arrives at the receiver socket before it's send on to the destination process. Other examples include data integrity and end-point authentication.

## Transport Services Provided by the Internet


## Application-Layer Protocols
## Network Applications Covered in This Book

# The Web and HTTP
## Overview of HTTP
## Non-Persistent and Persistent Connections
## HTTP Message Format
## User-Server Interaction: Cookies
## Web Caching

# Electronic Mail in the Internet
## SMTP
## Comparison with HTTP
## Mail Message Formats
## Mail Access Protocols

# DNS - The Internet's Directory Service
## Services Provided by DNS
## Overview of How DNS Works
## DNS Records and Messages

# Peer-to-Peer File Distribution

# Video Streaming and Content Distribution Networks
## Internet Video
## HTTP Streaming and DASH
## Content Distribution Networks
## Case Studies: Netflix, YouTube, and Kankan

# Socket Programming: Creating Network Applications
## Socket Programming with UDP
## Socket Programming with TCP