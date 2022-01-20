# Summary


# Notes
Users and devices should be authenticated separately, and device authentication shouldn't be conflated with user authentication. You can have scenarios where users have multiple devices, in which case you'd have to copy the device certificate to the other devices. You also can't guarantee the authenticated user is the one behind the keyboard.

## Identity Authority
 There are two types of identity: **authoritative** and **informal**.
 
 An informal identity is how groups self-assemble identity. It is things like what someone looks like and sounds like, how they dress and how they act, what their interests are. A persons informal identity is how you are able to recognize them when some parts of the identity are obscured, such as when they phone you and you can't see what they look like. People can assume an informal identity, for example when they sign up for a forum, and they can steal someone else's, for example by reusing someones username and avatar on another forum. A person can create several identities or multiple individuals can share a single identity.
 
 An authoritative identity is one created when a stronger form of identity is required for higher stakes. Examples of such identities include government issued IDs such as drivers licenses and birth certificates. Such a system must have a way for a person to regain control of their identity if it is lost, such as through identity theft.
 
 ## Bootstrapping Identity in a Private System
 Connecting a person to their digital identity for the first time is a sensitive process that must be done correctly. It's basically secure introduction for humans, and there are multiple ways it can be done:
 - **Government-Issued Identification** - this identification was designed for this very purpose, and the process can be made more secure by requiring multiple forms of identification.
 - **Human Interaction** - having a trusted individual be physically present for the bootstrapping process, such as a hiring manager escorting a new hire to collect and set up their laptop for the first time, reduces the risk introduced by a digital-based bootstrap. If the laptop was shipped in a "Trust On First Use" configuration, then there is a risk that the package can be intercepted and activated by an untrusted person.
 - **Expectations and Stars** - this is the process of collecting as many pieces of information as reasonable to identify an individual. It is similar to the network agent in a zero trust network, though the information is verified by a human. A background check on a new employee is a way some companies set expectations.

## Storing Identity
A centrally managed **User Directory** is the most practical way of storing user identities. It acts as a source of truth for who someone is and what they are allowed to do. This information is best not stored in a single data repository due to the sensitive nature of the information for making authorisation decisions, and the raw information itself should never be exposed. The data should be logically partitioned into different stores (the decision about how to split the data will be context specific) and these stores should only exposes very contrained APIs that allow clients to make assertions on the data contained within, without knowing what the data is.

Since this directory is the authoritative source of truth for the identity of all users in the organisation, it must be maintained to keep on top of people joining and leaving the company. The best situation would be an automated connection between the directory and the company's HR system. When someone leaves the organisation, their identity can be automatically deactivated, reducing the risk of people maintaining access to systems after leaving.

## When to Authenticate Identity
Security systems must be user friendly in order to be effective. Security measures that users find inconvenient to use will simply be circumvented, undermining the intentions of the masures in the first place.

### Authenticating for Trust
Tha amount of authentucation required to access a service or perform a task shoudl be proportional to the sensitivity of the servive/task. Logging into your bank account should require more authentication that logging into your subscription music service.

In a zero trust network, additional authentication can be used to boost a users trust score, either when performing a task requiring elevated privileges, or when their trust score has dropped below a certain threshold.

### Trust as the Authentication Driver
Instead of presenting arbitrary authentication requirements when performing tasks, a zerot rust network can instead define a minimum trust score required to carry out the task. If the authenticated user already meet the trust score requirement, they are able to proceed without additional authentication. If they do not meet the trust score requirement, then they will be presented with additional authentication.

### The Use of Multiple Channels
The use of multiple channels, either for authentication or authorisation, can be effective not because compromising any single channel is hard, but because compromising many is hard.

### Caching Identity and Trust
Asking a user to re-enter their TOPT every time they perform any task will be inconvenient and frustrating to the user, so their trusted session should be cached for some period of time. The longer a session is cached, however, increases the risk posed by changes in the user behavious as the control plane is unable to re-evaluate the users trust until the session has expired. The ideal situation is to have the users trust evaluated at each request, though this might not be practical in many situations.

When an application validates an SSO token, it should set the session tokens generated by the control plane, rather than generating its owen tokens. Using control plane tokens allows the control plane to revoke trust when behaviour changes, whereas using tokens generated by the application takes that control away.

## How to Authenticate Identity
The three most common factors used in authentication flows are:
- something you know, such as a password
- something you have, such as a hardware token
- something you are, such as a fingerprint

Which factors you require for authentication will depend on the situation. A user on a desktop or laptop computer might be best prompted for a password and a hardware token, where someone on a mobile device might be better off being asked for a pin number and a fingerprint.

### Something you know: Passwords
A good password has the following characteristics:
- it's long
- it's difficult to guess
- it is now reused

A password manager can make all of these easy, since remembering lots of unique passwords is a high bar for most users.

### Something You Have: TOTP
TOTP, defined in RFC 6238, can generate a code that is passed to the authentication service. If the code matches the one generated by the service, then it proves the user is in posession of the shared key.

There is a risk that this shared key can be stolen, for example if the user connects to a malicious endpoint that might extract the key. An alternative to using the TOTP algorithm is to have the service transmit a temporary code to the user via an encrypted channel that they user then replays to the service.

SMS is not an encrypted channel.

### Something You Have: Certificates
Generating a certificate from a strong private key then signing it with the organisations private key is another way to generate a credential that identifies a user. This certificate can hold a richer set of metadata on the user since it is being consumed by computers, not people.

The problem with this is that it relies on the certificate being stored securely. It is recommended to generate and store the private key and certificate on dedicated hardware to prevent theft of the key.

### Something You Have: Security Tokens
Physical hardware tokens such as yubikeys can tie a users identity to a particular piece of hardware by storing the private key in the hardware. In order to steal the users identity, the physical theft of the hardware token would be required.

This isn't an alternative to the other authentication factors, since these devices can still be stolen. They should instead be used alongside at least one other factor.

### Something You Are: Biometrics
Biometrics such as fingerprints are becoming more common in authentication as the devices for reading them are making their way into more consumer technology. While a fingerprint is a strong way of identifying a user, it isn't a foolproof way of doing so.

Fingerprints can be stolen since we leave them on everything we touch. It has been shown that these can be photographed and replicated via a 3D printer before being used to successfully trick a scanner.

There can also be accessibility issues, since people can be born without fingerprints, or can have lost their finders in an accident.

### Out-of-Band Authentication
Out-of-Band authentication is where additional authentication of the user is carried out over a different channel from the original authentication request. For example, a user might log into a system, and received a phone call with a TOTP to complete the process.

### Single Sign On
SSO allows authentication to be decoupled from end services and reduces the amount of authentication that needs to be performed when a user is interacting with multiple services.

The recommended approach is to have the end services validate the SSO tokens against the central identity service as often as possible. This allows for vairance in trust scores to be reflected in a users access in a timely manner.

Authentication in a zero trust network should always be a concern of the control plane. End services should not be carrying out their own authentication.

### Moving Toward a Local Auth Solution
It is possible to have users authenticate with end services at a local level while still keeping authentication a concern of the control plane. Users can pass their credentials to a trusted local device which then attests to that identity with the remote identity service.

This local device acts like a password manager, but instead of storing passwords it stores private keys. The user unlocks the device and privides their private key for authentication.

This provides a number of benefits:
- replay attacks can be mitigated vi a challange and response sytem
- man in the middle attacks can be twarted by having the authentication service refuse to sign the challenge unless it originated fromt he same domaint he user is visiting
- credential reuse is non-existent since per-service credentials can be trivially generated.

## Authenticating and Authorizing a Group
As the level of trust inplicit in a system approaches zero, the amount of trust placed in a single individual also reaches a lower limit, where authentication needs to be carried out with two or more users.

### Shamir's Secret Sharing
This is an algorithm for splitting a secret and `n` parts which can then be distributed to a group of individuals. Depending on how the algorithm was configured when the parts were generated, `k` parts are needed to recalculate the original value.

### Red October


## See Something, Say Something
The traditional role of security teams "owning" security on behal of the organisation has typically lead to the degradation of security since an antagonistic relationship has usually formed between security and the rest of the organisation.

Users of the system should be encouraged to be active participants in the security of the system, reporting anything they think is suspicious, such as phishing emails. They should also be encouraged to report issues affecting themsevles, such as losing a device, without fear of being shamed.

## Trust Signals
There are a number of signals that can contribute to the assessment of a users trust:
- historical behaviour, for example authentiticating orders of magnitude more times than they have done in the past
- origin of the traffic, for example authentication attempts orginating from known sources of malicious activity
- geolocation, for example comparing the users current location to a list of previous locations