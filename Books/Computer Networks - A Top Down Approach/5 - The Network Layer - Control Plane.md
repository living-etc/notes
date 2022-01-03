

# Routing Among the ISPs: BGP
BGP is an inter-AS routing protocol.

## The Role of BGP
When a router is forwarding packets to another AS, it doesn't forward them to a specific address, rather to a CIDRized prefix, such as 138.16.88/22. The routers forwarding table would contain an entry of the form (x, l) where x is the prefix and l is the interface number of one of the routers interfaces.

BGP fulfills the following roles:
- **Obtain prefix reachability information from neighboring ASs**. BGP allows each subnet to advertise their existence to the rest of the internet. Without BGP, each subnet would be an isolated island with no traffic able to move between them.
- **Determine the "best" routes to the prefixes**. A router will use reachability information as well as policy information to determine the best route to  specific prefix when two or more are available.

## Advertising BGP Route Information
Consider the network shown below:
![[Screenshot 2021-12-11 at 17.24.57.png]]

The **gateway routers** in the ASs send messages to each other to announce routes to the different ASs they know about. These gateway routers connect the AS to other ASs, while **internal routers** only connect to hosts within the AS.

Routing information is exchanged over semi-permanent TCP connections, called **BGP connections**, using port 179. A BGP connection spanning two ASs is called an **external BGP connection (eBGP)** while such a connection between hosts in the same AS is called an **internal BGP connection (iBGP)**.

In the diagram above, routing announcements travel over both external and internal BGP connections so that each router knows about the routes between the three ASs.

## Determining the Best Routes
In BGP, a prefix along with its attributes is called a **route**, the two most important of which are:
- **AS-PATH** - the list of ASs through which the advertisement has pass. Routers are configured to reject advertisements that contain their own AS to prevent looping advertisements
- **NEXT-HOP** - The IP address of the router interface that begins the AS-PATH.

### Hot Potato Routing
Hot potato routing is where the least cost route is chosen, where cost isn't necessarily about money. It could, for example, mean the fewest hops or the lowest ping time. A router calculates the least cost path to a link and adds it to its forwarding table. Hot potato routing is named after a hot potato that is burning your hand, and you want to pass it on to the next person as quickly as possible.

When adding an outside-AS prefix to the forwarding table, both the intra-AS routing protocol (BGP) and inter-AS routing protocol (OSPF) are used.

Hot potato routing is a selfish routing algorithm where routers prioritize reducing costs to their own AS while ignoring the end-to-end-cost out the AS. Even if there are multiple routes out of an AS to the destination, a router won't consider anything other than the cheapest option with respect to its own situation.

### Route Selection Algorithm
