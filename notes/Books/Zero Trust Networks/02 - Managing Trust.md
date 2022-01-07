# 2 - Managing Trust
In a network without any explicit trust, implicit trust must be sourced from somewhere, and that is the operator who deploys and manages the system. The operator can't always be around to perform every action, so they must delegate trust to systems that will do so on their behalf. 

This is called a _trust chain_ and the operator is called a _trust anchor_.
![[delegation of trust.png]]

## Threat Modeling
Based on attacker-based threat modeling techniques, we can categorize possible attackers into the following in increasing order of sophistication
   - Script-kiddies
   - Targeted attackers
   - Insider threats
   - Trusted insider
   - State-level actor

Zero Trust Networks follow the Internet Threat Model, described in RFC 3552, to plan their security stance. This threat model assumes that end systems engaging in protocol exchange are secure and uncompromised, since we can't control those endpoints. It does assume that attackers on the internet itself can intercept traffic traveling between secure end systems, sending packets on to the destination that appear to be from a secure end system. Thus, you cannot guarantee that the packets arriving at your own system are originating from where they claim.

The zero trust threat model expands on this, since it's applied to a system where the operators control all the endpoints. A zero-trust network is designed to mitigate attacks against threats up to the level of trusted insider. Most organisation don't face a threat from state-level actors so this model doesn't try to mitigate their attacks, though it does go some way to mitigation attacks from these actors attacking the network remotely. State level actors are assumed to have access to significant resources that make their sophisticated attacks difficult to defend against.

## Strong Authentication
In order to establish trust with any client on the network, they must prove they are who they say they are. Humans are able to do this when speaking face to face with someone, using multiple senses to determine how much we should trust this person in front of us. If we hear them claim to be someone, we can verify that with our eyes. On the other hand, if we are speaking to them over the phone, we do not have visual confirmation of their identity claim, and this is the same problems computers have.

Traditionally, IP addresses and passwords have provided the authentication method, but this is not enough for a zero trust network when attackers can communicate from any IP they please and insert themselves between yourself and trusted remote host.

Strong Authentication is best implemented using a X.509 certificate. The question is should you use PKI or roll your own private certificate authority? 

Since certificates make use of public key cryptography, the successfuly decryption of a message using the public key confirms that the sender is in the presence of the secret part of the key. It does not, however, account for the possibility of the secret being stolen, which can be countered by requiring multiple secrets for authentication. Since multiple secrets only makes it harder to steal all the necessary components of authentication, not impossible, the secrets should be time-bounded with an expiry.

## Authenticating Trust
Having a public key isn't enough. You need to know you have the right public key, and this can be done using Public Key Infrastructure. PKI using a Registration Authority to embed an identity into a certificate, which is cryptographically signed by a trusted third party. This certificate can be presented as proofy of identity if the recipient also trusts the same third party.

There are some downsides to using PKI:
   - Cost, since the CAs are for-profit businesses, though Let's Encrypt might eliminate this problem
   - Lack of programmable interfaces, since you might want org-specific metadata encoded in your certificates
   - It's hard to fully trust the public certificate authorities

Despite these, PKI is still better than nothing at all.

## Least Privilege
Users and applications should carry out their normal actions on a network in an unprivileged mode with access only to the minimal amount of resources they need. Additional privileges should be granted only when they are requests, be time-bounded, and require additional authentication such as another token or additional approval some another individual.

Zero trust networks also consider the privilege of the device being used to authenticate, combining the user authentication and device authentication to determine how much trust the user should receive. For example, a user logging into their company portal through their browser on their personal laptop might be given fewer options than one logging in from a managed corporate machine.

Changes in privileges must be dynamic and easy to do. Typically, policies defining user permissions are static. Whenever someone needs to elevate their privileges, they must lobby for a change in the policy, which is implemented permanently to the policy, increasing their permissions over time and reducing the effect of least privilege. Additionally, the sysadmins making the changes usually have high-level permissions, often administrator permissions, to all systems, further reducing the impact of a least privilege stance in the organisation.

Zero trust networks being able to dynamically increase permissions on a time-bound basis makes them more secure in the long term.

## Variable Trust
In the same way a credit score from a credit agency can be used to make decisions on the trustworthiness of an individual to a credit lender, a trust score can be computer for clients on a network based on their pased behvaiour. This score can be used to determine their overall trustworthiness for the purposes of elevating privileges.

A client would connect to the network initially with no trust. Doing to from a company-owned computer would increase their trust score. Prividing the correct RSA token would increase it further.

This model is not without its drawbacks.
- Since scores can go up and down over time, does that mean a persistent attacker can improve their trust score if they are patient enough?
- How do users who need higher privileges to do their job, such as sysadmins, get them in a timely manner?
- How does the system communicate to an end user that they are not trusted to access a system from a coffee shop but they are from their home network?

## Control Plans vs Data Plane
In networking, the data plane is responsible for the transport of traffic through the network while the control plane is responsible for the signalling traffic and logic for managing the network.

The same concept exists in a zero trust network, where the date plane is responsibile for the flow of traffic between clients and resources, and the control plane is responsible for authenticating and authorising requests to the network, and establishing and managing trust in the clients.

As such, the security of the control plane is critical to the functioning of a zero trust network. The interface between the control and data planes should resemble that of the kernel/user space, where interactions are heavily isolated to prevent privilege escalation.