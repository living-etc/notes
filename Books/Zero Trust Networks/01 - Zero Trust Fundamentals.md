# Summary


# Notes
A zero trust network is a response to the flaw inherint in a traditional layered network. In this traditional model, the network within the perimiter firewall is fully trusted, and so is any actor within that network. The problem with this is that if a malicious actor is able to gain access to your trusted network, they have the trust needed to move laterally throughout the network, attacking any systems within it.

## Traditional Network Design
A traditional network design makes the following assumptions:
- the perimiter defences are impregnable
- any actor on the trusted internal network is supposed to be there

![[traditional layered network design.png]]

The problems with a traditional network design are
- traffic is only inspected on the border of each zone. Once traffic is inside a zone, it is free to go anywhere within.
- the gateways between each zone act as a single point of failure

## Zero Trust Network Design
Zero trust networks, on the other hand, make the following assertions:
- the network is always assumed to be hostile
- external and internal threats exist on the network at all times
- network locality is not sufficient for deciding trust in a network
- every device, user and network flow is authenticated and authorised
- policies must be dynamic and calculated from as many sources as possible

![[zero trust network design.png]]

Zero trust network designs solve the problems of traditional network by:
- having all access to protected resource be made through the control plane where both user and device are authenticated
- having the control plane configire the path to the resource that can be used by that client and that client only

## Key components in a zero trust network
1. user/application authentication
2. device authentication
3. trust score

These three are bonded to form an agent, against which a policy is applied. The score is factored into the policy to allow fine-grained and flexible access control that can adapt to changing situations.

The traditional model was one of hard cells with soft bodies inside, and the zero trust model is about creating hard bodies. VPN is an example of the kind of workflow that would be implemented throughout the network.