# What is the Internet?
There are two ways of describing the internet.

The first is a nuts and bolts description that includes the individual components of the internet.

The second is a services description, where the internet is an infrastructure that provides services to applications.

# The Network Edge
The part of a network that includes the end systems connecting to it. The sources and destinations of traffic reside at the network edge. This includes laptops and smartphones on the client side, and data centres on the server side.

## Access Networks
An access network links the end system to the first router (also known as an Edge Router) on the path to its destination.

### Home Access: DSL
One of two forms of residential internet connection that is provided by a customer's telephone provider.

It carries both telephone and internet data signal simultaneously. It carries data in the following bands:

-   A high-speed downstream channel between 50kHz and 1MHz
-   A medium-speed upstream channel between 4kHz and 50kHz
-   An ordinary two-way telephone channel between 0 and 4kHz

On the customer side, a splitter separates the phone and internet signals while on the telco side a DSLAM (Digital subscriber line access multiplexer) separates them.

A DSLAM is a device that interfaces the multiple networks of the internet and telephone lines with the single connection of the residential internet connection. It takes multiplexed signals from the residential end and separates them for use on separate telephone and internet networks. Conversely, it takes the separate signals from the telephone and internet networks and combines (multiplexes) them for transmission along the residential DSL connection.

Thousands of homes can be connected to a single DSLAM
![[dsl.jpg]]

### Home Access: Cable

One of two forms of residential internet connection that is provided by a customer's cable television provider.
![[cable.jpg]]

Cable Modem Termination System (CMTS) - equivalent to a DSLAM on a DSL internet connection. A device located in the cable head end of a cable TV network. It takes the analogue signal received from a the cable modem in a downstream home and turns it into a digital signal.

Data Over Cable Service Interface Specification (DOCSIS) - An international telecommunications standard that permits a higher data transfer rate over existing cable television networks. Each new version of this standard defines a higher data transfer rate.

### Home Access: Fibre To The Home (FTTH)
![[fibre to the home.jpg]]

Optical Line Terminator (OLT) - The Fibre To The Home (FTTH) equivalent of the DSLAM on a DSL network or a CMTS in a cable internet network.

Optical Network Terminator (ONT) - The equivalent of the router in a Fibre To The Home (FTTH) network.

### Enterprise Access: Ethernet
![[ethernet.jpg]]

# The Network Core
The part of a network that includes the switching and routing components. This part of the network is responsible for routing traffic between different end systems on the network edge. For example, traffic will leave a smartphone on the edge, travel through the routing infrastructure of the core, and arrive at a server on another part pf the edge.

Edge Router - A router on the edge of the Network Core. This router is the first router through which traffic from an end system will travel on its way to its destination.
![The Network Core is highlighted here in green](the network core.jpg)

The Network Core is highlighted here in green

## Packet Switching
In a Packet-Switched Network, packets are sent onto the internet with no resources reserved for their transmission. Instead, packet switches will route packets to their destination as they arrive on the input link, but with the potentials for delays or packet loss.

For example, if the network is congested and the switches output buffer is full, it will drop packets, either from the input link or the output buffer. This will result in packet loss where the packets are dropped, and delays where the packets are queued before being transmitted.

### Store-and-Forward Transmissions
![An Illustration of store-and-forward packet switching. When the switch receives the first bytes of a packet, it will buffer them until it has received all the bits. At this point it will begin transmitting the entire packet.](store-and-forward transmissions.jpg)

An Illustration of store-and-forward packet switching. When the switch receives the first bytes of a packet, it will buffer them until it has received all the bits. At this point it will begin transmitting the entire packet.

Assume the following:

-   L: Number of packets to be transmitted
-   R: Transmission rate of the link along which the packets are being transmitted
-   N: The number of links along the transmission path

The end-to-end delay in transmitting this entire message is

$d\_{end-to-end}=N(L/R)$

### Queuing Delays and Packet Loss
![[queuing delays and packet loss.jpg]]

The output buffer of a packet switch is the queue where packets wait to be transmitted. If a packet is ready to be transmitted, but the output link is busy, it will wait in the queue.

If the queue is full, then either the packet in question, or one of the already queued packets, will be dropped, leading to packet loss.

### Forwarding Tables and Routing Protocols
Each router contains forwarding tables that it uses to determine where to send the packet. It compares the destination address (or a part of it) of the packet to the entries in its forwarding table for forwards the packet along the relevant link.

The internet has a number of routing protocols that are used to automatically populate these forwarding tables.

## Circuit Switching
A type of network where an end-to-end connection between the source and destination is established before a message is sent. The resources between the source and destination (switches and links) along the path have a portion of their capacity reserved for the transmission of the message.

Contrast this with a Packet-Switched Network, such as the Internet, where packets are sent onto the network without resources specifically allocated to it, and the network endeavors to get the packets to their destination as fast as possible, or at all.

![[circuit switching.jpg]]

### Multiplexing in Circuit-Switched Networks
-   Frequency-Division Multiplexing - The bandwidth of the link is divided into individual bands for the exclusive use of the connections
-   Time-Division Multiplexing - Each connection is allocated time slots during which its data will be transmitted across the link

![[multiplexing in circuit-switched networks.jpg]]

The difference between these two forms of multiplexing is that with FDM, each circuit gets a fraction of the bandwidth all of the time, whereas with TDM, each circuit gets all of the bandwidth for a fraction of the time.

### Circuit Switching vs Packet Switching
Drawbacks of circuit switching:

-   The resources allocated to a connection are wasted during silent times. For example, when someone on a telephone call stops talking, the bandwidth cannot be used by other connections for the duration of the silence.
-   Complicated signalling software is need to co-ordinate the persistent connections and the activities of the packets along the path

Drawbacks of packet switching:

-   Not suitable for real-time services links telephone calls and video conferencing because of the variable and unpredictable transmission rates, due to variable and unpredictable queuing times.

Benefits of circuit switching:

-   Fixed connection with allocated bandwidth results in predictable transfer speeds and a more stable connection

Benefits of packet switching:

-   Better utilisation of connection resources, such as bandwidth
-   Simpler to understand and implement

## A Network of Networks
| ![[interconnection-of-isps.jpg]] |
| -------------------------------- |
| Interconnection of ISPs          | 

**Point of Presence (PoP)**

A group of one or more routers on a provider's network where customer ISPs can connect to the provider ISP.

**Multi-home**

An ISP is said to be multi-homing when it connects to two or more provider ISPs. This allows the customer ISP to continue to send and receive traffic from the internet even when one of its providers suffers a failure.

**Peering**

Two ISPs can choose to connect their networks together in a process called peering. This will allow them to transfer data between each other without paying an intermediary provider ISP to do so. ISPs at the same level of the hierarchy will do this settlement-free, that is they don't day each other for transit across each other's network.

**Internet Exchange Point (IXP)**

A point at which multiple ISPs can peer together provided by a third-party company, typically in it's own building with it's own switches. There are currently over 400 such points on the internet today.

**Content-Provider Network**

A network operated by an internet content provider, such as Google. Google's network spans the world and attempts to bypass the tier-1 ISPs by peering directly with lower tier networks settlement free. Since these lower-tier access networks can only be accessed by the higher tier transit providers, Google peers with these tier-1 ISPs and pays the settlement for transit.

A content-provider network allows a content provider to better control how its content is distributed to end systems.

# Delay, Loss, and Throughput in Packet-Switched Networks

## Overview of Delay in Packet-Switched Networks
![[delay in packet switched networks.jpg]]

As a packet passes through the nodes along it's path, it suffers the following types of delay at each node:

-   **nodal processing delay** - the delay added by inspecting the headers of the packet to determine it's destination. Error checking the integrity of the packet also contributes to this delay. Typically on the order of microseconds in high-speed router.
-   **queuing delay** - the delay added while the packet is waiting in the queue at the output link. This delay is a function of the intensity and nature of the traffic arriving at the router. Typically on the order of microseconds to milliseconds in practice.
-   **transmission delay** - the delay added as the packet is taken from the output queue and pushed onto the output link. Can be calculated as L/R, where L is the length of the packet in bits and R is the transmission rate of the output link. Typically on the order to microseconds to milliseconds in practice.
-   **propagation delay** - the delay added after the packet is transmitted until it reaches the beginning of the input link at it's destination. The packet propagates at propagation speed of the link, and the propagation delay measured as d/s, where d is the distance between the two routers, and s is the propagation speed of the link. Can be typically on the order of milliseconds in wide area networks.

Together these accumulate to give the **total nodal delay:**

$d\_{nodal}=d\_{proc}+d\_{queue}+d\_{trans}+d\_{prop}$

## Queuing Delay and Packet Loss

Queuing delay is the most significant and interesting of all the delays because it can vary from packet to packet. If 10 packets arrive at a route at the same time, the first packet will suffer no queuing delay, while the last will suffer a significant queuing delay. Thus, queuing delay needs to be characterized with statistical measure, such as average and variance.

To be able to estimate the queuing delay, we need to understand the intensity of the traffic arriving at the router. This can be measure as:

$La/R$

where:

-   L is the average size of a packet in bits
-   a is the average rate at which packets arrive in the queue
-   R the rate at which packets are pushed out of the queue (transmission rate)

When this ratio is greater than 1, then the average rate at which packets are arriving in the queue exceeds the rate at which they are being transmitted, and the queue delay will increase without bound until it reaches infinity.

| ![[average queing delay as a function of traffic intensity.jpg]]                                                                                                                   |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Average queuing delay as a function of traffic intensity. Note that small increases in the traffic intensity ratio can result in larger (percentage-wise) increase in queue delay. | 

Average queuing delay as a function of traffic intensity. Note that small increases in the traffic intensity ratio can result in larger (percentage-wise) increase in queue delay.

> One of the golden rules of traffic engineering is "design your system so that the traffic intensity is no greater than 1".

When this ratio is less then or equal to 1, the nature of the traffic will determine the queuing length. For example, of the packets arrive periodically, that is one packet arrives every L/R seconds, there will be no queuing delay since every packet will find an empty queue.

On the other hand, if the packets arrive in bursts, then first packet transmitted will experience no queuing delay, while the last one will experience a significant queuing delay.

In reality, traffic arrives at the router randomly, so the queue delay is effectively random. Also in reality, queue lengths are not infinite, which we assumed for the purpose of the above description. When a packet arrives at a router to find a full queue, the router will drop that packet. This is known as packet loss.

## End-to-End Delay

The nodal delay discussed about is the delay incurred at a single router. The end-to-end delay is the delay incurred at all routers along the path:

$d\_{end-to-end}=N(d\_{proc}+d\_{trans}+d\_{prop})$

In this equation, we assume the network is uncongested so that queuing delays are negligible.

# Protocol Layers and Their Service Models
| ![[protocol layers as an airline flight.jpg]]                                          |
| -------------------------------------------------------------------------------------- |
| Analogy to the internet routing protocol layers using an airline flight as an example. | 

![[five and seven layer models.jpg]]

## Layered Architecture

**Application Layer**

The layer where network applications live. Protocols such as HTTP and FTP are implemented here, and applications such as DNS run here. Packets in this layer are called **messages**.

**Transport Layer**

The transport layer transports application-layer messages using one of two transport protocols: TCP and UDP. Packets in this layer are called **segments**.

The behaviour of the traffic will be determined by the transport protocol chosen. For example, TCP implements congestion control mechanisms and guarantees delivery of packets to the destination. UDP, on the other, does non of these things.

**Network Layer**

The layer that is responsible for routing transport-layer segments from one host to another. Packets in this layer are called **datagrams**.

This layer contains the IP protocol, which defines the fields in the datagram as well as how the end systems and routers act on those fields. It also implements the various other routing protocols in use on the internet. Every device that runs a network layer must implement the IP protocol.

**Link Layer**

The layer that moves a network-layer datagram from one node on the network to another. The services provided by link-layer protocols differ from one protocol to the next. For example, some link-layer protocols provide reliable delivery of packets from one node to the next. This should not be confused with the reliable delivery of the TCP protocol, which guarantees delivery between end-systems.

A packet may be handled by multiple link-layer protocols on it's journey between end-systems, some examples of which are Ethernet and Wifi.

Packets in the link layer are called **frames**.

**Physical Layer**

Responsible for moving the individual **bits** of a link-layer frame from one node to the next. The protocols here are further dependent on the transmission medium of the link. For example, ethernet has a different physical-layer protocol for twisted-pair copper wire, coaxial cable, fiber, etc.

**The OSI Model**

The OSI model was an alternative model in the late 1970s which is not in use today, but is still useful to understand how networks function. Five of it's layers had similar functionality to those of the IP stack described above. The other two layers provided the following functionality:

-   **Presentation Layer** - Was responsible for the interpretation of the format of the data being transmitted, so that applications didn't have to worry about this themselves.
-   **Session Layer** - Provided for delimiting and synchronization of data exchange

The absence of these two layers in the modern IP stack implemented by the internet means the services they provide are left up to the application developers.

## Encapsulation
| ![[encapsulation.jpg]] |
| ---------------------- |
| An illustration of a packet moving up and down the stacks of the various devices on it's path. Each device is adding it's own header information before passing it to the next layer down in the stack (encapsulation). When moving up the stack, the header information for the current layer is removed and interpreted and the payload is passed up to the next layer (de-encapsulation). Thus, at each layer in the stack, a packet has two types of fields: a header field for the current layer, and a payload containing the original message and the header fields from the upper layers of the stack.                       |

# Networks Under Attack

# History of Computer Networking and the Internet

## The Development of Packet Switching (1961-1972)

## Proprietary Networks and Internetworking (1972-1980)

## A proliferation of Networks (1980-1990)

## The Internet Explosion (1990s)

## The New Millennium