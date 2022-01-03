# 8 - Trusting the Traffic
## Encryption vs Authentication
Encryption ensure that a message can only be read by the intended receiver, and authentication allows the receiver to assert that the sender is what they claim to be.

### Authenticity Without Encryption
Omitting encryption from network traffic is rarely justifiable since it is rare to find traffic that doesn't require confidnetiality. There are few network protocols that provide strong authentication without encryption, so encryption can be seen as coming for free.

## Bootstrapping Trust: The First Packet
A problem we have with authenticating network flows is how to we allow connections from authorised clients while silently dropping connection attempts from unauthorised clients? If we have an endpoint that needs to be accessed by clients from the internet, we don't want to reveal its existence to potential attackers by responding to every connection attempt.

This is called the **first packet problem**, and is solved by a method called **pre-authentication**. Before an authorised client attempts to establish a TCP connection to the server, it first sends a UDP packet signed with its private key. Being signed indicates it is coming from an authorised client, and being UDP no response is required. The server silently opens a channel through the firewall, allowing the authorised client to connect to it. Any unknown client will not receive a response from the server since it did not send a pre-authentication packet. This mode of pre-authentication operation is called **Single Packet Authorisation (SPA)**.

SPA is not a substitute for strong device authentication. It only downplays the first packet problem.

![[Screenshot 2021-05-17 at 06.17.41.png]]

### fwknop
**fwknop** is an open source SPA implementation. It integrates with host firewalls to create tightly scoped rules that allows only the senders IP address to pass through a specific port. The configuration is removed after a certain time, typically 30 seconds.

The connection details for the flow are contained in the SPA packet, including:
- 16 bytes of rnadom data
- local username
- local timestamp
- fwknop version
- SPA message type
- access request (which port, etc)
- SPA message digest (SHA-256 by default)
- **HMAC** (hashed message authentication code), optional

It is strongly recommended to use HMAC with fwknop.

## A Brief Introduction to Network Models
### TCP/IP
![[Screenshot 2021-05-17 at 06.53.35.png]]

### OSI Network Model
1. **Physical Layer**: defines the interface between network hardware and the transmission medium, including things like voltage, pin layout, etc
2. **Data Link Layer**: responsible for the transmission of data over the physical layer. Only considers transmission between directly connected nodes. Doesn't know about interconnected networks. **Ethernet** is the most well known protocol operating at this layer.
3. **Network Layer**: responsible for transmitting packets between two interconnected networks, potentially over multiple layer 2 segments. Includes concepts to allow routing data to its destination by inspecting packet details such as IP address.
4. **Transport Layer**: augmenting layer 3 protocols with desirable services. TCP and UDP are both layer 4 protocols. Some of these services include:
	1. Stateful connections
	2. Multiplexing
	3. Ordered delivery
	4. Flow control
	5. Retransmission
5. **Session Layer**: Provides an additional layer of state over connections, allowing for communication resumption and communication through an intermediary. Several VPN (PPTP, L2TP) and proxy protocols (SOCKS) operate at this layer.
6. **Presentation Layer**: Responsibile for handling the translation between application data and transmittable data streams. TLS operates at this layer.
7. **Application Layer**: Provides the high level communication protocols that an application uses to communicate on the network. Some common protocols include HTTP, DNS and SSH.

### TCP/IP Network Model
TCP/IP defines several layers that can roughly map to the OSI layers:
- Link Layer
- Internet Layer: layer 3
- Transport Layer:  maps to layer 4, though includes the concept of a port which gives it some layer 5 characteristics
- Application Layer: covers roughly layers 5-7

TCP/IP doesn't try to create strict boundaries between the layers, and in fact [considers them harmful](https://www.ietf.org/rfc/rfc3439.txt).

## Where Should Zero Trust Be in the Network Model
Of the two predominant network security suites (TLS and IPsec) IPsec is the prefered option for encrypting network flows. TLS is found in the application layer of the TCP/IP model (between layers 5 and 6 in the OSI model), meaning it needs to be configured on a per-application basis. This is not sufficient for a zero trust network since we need to ensure that each network flow is authenticated. IPsec, on the other hand, operates at the Internet Layer of TCP/IP (layer 3 or 4 in the OSI model), and thus can be build into the fabric of our network ensuring that all flows are encrypted and authenticated by default, without any intervention from higher layers.

For ensuring that each individual network flow is authorised, there are the following options:
- IPsec can use a unique [security association](https://datatracker.ietf.org/doc/html/rfc4301#section-4.4.1.1) (SA) per application.
- Filtering systems, such as software firewalls, can be layered on top of IPsec.
- Application-level authorisation should be useds to ensure that communications are authorised, which encryption and authentication responsibilities are delegated to IPsec.
- Mutual TLS and IPsec could be layered on top of each other for a defence in depth approach, at the expense of complexity.

### Client and Server Split
Despite its beneficial properties, IPsec's lack of real world popularity leads to the following problems:
- **network support issues** - few networks in the wild, including AWS, support the **ESP** and **AH** protocols used by IPsec.
- **device support issues** - IPsec has multiple configuration options that must match on both the client and server before they can communicate, and not all cipher suites might be implemented on one or the other.
- **application support issues** - IPsec needs to be enabled and configured in the host kernel, whereas TLS is implemented via a library. This means any updates, such as applying security patches, can be more time consuming since it means updating the kernel then finding the application-specific configuration that needs changed.

### A Pragmatic Approach
For client-server interactions, mutual TLS would be the most sensible approach where a browser is configured to present client certificates to server-side access proxies. For server-server interactions, where the server fleet are generally under more controlled configuration, IPsec would be reasonable, with UDP encapsulation where IPsec isn't supported.

The IPv6 standard includes IPsec, so it is hoped that it will become more commonplace in the future.

## The Protocols
It's important to understand the inner workings of both TLS and IPsec since they are both complictaed to configure, and this insecure configurations are common.

### IKE/IPsec


### Mutually Authenticated TLS


## Filtering
 Filtering is the act of accepting or rejecting traffic entering or leaving the network, and can be done in one of three ways:
 - **Host filtering** - Filtering of traffic at the host
 - **Bookended filtering** - Filtering of traffic by a peer host in the network
 - **Intermediary filtering** - Filtering of traffic by devices in between two hosts.

### Host Filtering
This is where filtering happens at a software firewall on the host instance, the destination for the traffic. The benefit of this is that sofwtare firewalls are more flexible than hardware-based ones, since hardware firewalls make use of features of the hardware fo perform the filtering in a performant way.

There are, however, a number of drawbacks to this model:
- an attacker who gains access to the host can disbale its firewall, rendering the host defenceless if this is the only filter available. This problem can be mitigated by making use of hypervisor-level filtering, such as EC2 security groups.
- allowing the traffic deep into the network before dropping it can lead to additional costs of running the network, as well as allowing for denial of service attacks as the traffic can overwelm other parts of the network before it reaches the host

### Bookended Filtering
This is where both ingress and egress rules are used to harden the internal network. As well as a host having ingress rules for trafic allowed to reach it, it also has egress rules for the traffic its allowed to send to other hosts. In this scenario, not only must traffic be authorised to leave its source, it must also be authorised to enter its destination. This is like herd imunity for the network. A misconfiguration in the ingress rules for the database host won't necessarily allow previously unauthorised hosts to communicate with it since their egress rules won't allow it.

### Intermediary Filtering
Filtering at a point between the source and destination, including the network perimeter, is a way of reducing the traffic load on the destination host. Perimeter filtering is still a valid thing to do in a zero trust network since zero trust networks don't throw away all perimeter concepts, it just encourages administrators to start at the host and work outward when considering security.

Perimeter filtering solves the problems introduced by allows large volumes of malcious traffic into the network to be filtered at the host. Filter out the worst, most obvious traffic, at the perimeter and let the hosts get the rest.

### Forwarding and Routing Authorisation
Imagine an SDN controller that installed flow instructions on the firewall (either host or perimeter) only after successful authentication and authorisation of a client. The clients presents their credentials (device, user and application) to the control plane which singals the SDN controller to open up a route through the network to the requested host for a time limited period.