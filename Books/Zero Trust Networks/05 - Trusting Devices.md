# Summary


# Notes
## Bootstrapping Trust
A new device needs to have its trust injected into it, and this is done with a golden image whos software is all verified, bringing the device into a known trusted state. Its identity can then be added by provisioning a certificate signed by the organisations private certificate authority.

### Generating and Securing Identity
Once the certificate has been generated and provisioned, its corresponding private key must also be protected. The most effective way to do this is with either Hardware Security Module (HSM) or Trusted Platform Module (TPM), components of the hardware that carry out cryptographic operations. The private key is stored in this module, and the operating system has no access to it, making it very difficult to steal the private key.

### Identity and Security in Static and Dynamic Systems
The process for signing certificates is one that must be secured so unauthorized certificates are not being generated. Having a human involed to approve a signing/provisioning workflow works well enough in a static environment.
![[authorisation - certificate signing - manual.png]]

In dynamic environments, where systems scale up and down on their own, this process must be automated. In a static environment, the trust needed to sign the certificates was sourced from the human operator, but that isn't possible in an environment where the process is entirely automated.

One option is to bake generic authentication material into the device image which is used to securely communicate with a signing service to generate a device-specific certificate. In this way, trust is now derived from the image in the absence of a human operator.

Another source of trust is the resource manager, the system that launched the new devices. It's always able to assert "yes, i launched this device and here is everything I know about it". This information can be passed to the signing service.

Combing the two (image and resource manager) is the best way. If an image is stolen, it can't be used to generate a certificate since the approval of the resource manager is missing, and the resource manager doens't have access to the authentication material on the image to request a certificate.

## Authenticating Devices with the Control Plane
### X.509
Using certificates signed by a certificate authority (with public or private) allows us the convenience of only distributing the public key of the CA in advance, rather than a public key for every certificate.

The private key component of the certificate must be kept secure or else that device will become vulnerable if it is leaked. We can encrypt the key on the device and prompt for a password to decrypt it at time of use, but this only works for a user-facing device. On an autonomous system, we would still have to store the password somewhere, which just moves the problem around. HSM goes a long way to solving this problem for us.

The ultimate problem we are trying to solve here is that we are using software (the private key) to authenticate hardware. While it is difficult to steal the private key, it is not impossible since it is not inextricably married to the device, and that is a problem that TPMs (Trusted Platform Modules) can solve.

### TPMs
A TPM is a component of the device hardware that is designed into it by the manufacturer. It is not feasible to install such a component after the device has been manufactured. It performs the same function as a HSM except it has a private key burned into it, marrying that key to that device.

The TPM generates what's called a Storage Root Key (SRK) which represents the trust root for all data encrypted with it. This key can then be used to generate intermediate keys that can only be decrypted and used if in the presence of the SRK that created it, which means it can only be used on the trusted device.

TPM can be used to encrypt the private key of the X.509 certificate, and only decrypt each time it is used. If the key is stolen from the device, it is unable to be decrypted and is thus useless. This protects the key being read from disk, but doesn't prevent an attacker with elevated privileges from either reading it from memory or asking the TPM to decrypt it from them.

**Platform Configuration Registers** (PCRs) are a feature of a TPM that stores a hash of all running software, starting from the BIOS and working up. Data encrypted using the TPM is done so in the presence of certian hashes from the PCR, a process known as sealing. Once sealed, the data can only be decrypted using the same hashes, meaning it can only be decrypted using a known good software configuration.

Unless the device's private key is stored on the TPM, it is vulnerable to theft as an attacker only has to convince the TPM to decrypt it once to disclose the key itself, something that wouldn't be possible if it were stored on the TPM. To solve this problem, the TPM contains another keypair called the **Endorsement Key** (EK), the private component of which only exists on the TPM, making it inaccessible to the operating system. The TPM sends a **quote** to a remote party, containing a list of current PCR values, signed with the EK, in a process known as **Remote Attestation**. This party can use this to assert both the host identity (since the EK is unique to each host), and the software configuration of that host (since PCR values can't be changed).

The reson a X.509 certificate is used at all, and not just the TPM key, is that TPM access is cumbersom and non-performative. Relying on it for all device authentication workflows will likely add significant latency to all requests.

TPM, despite having many strenghts for use in a zero trust network, also has limitations. It is tied to hardware, which makes it difficult to use in a virtualised environment. It also is not widely adopted. It should not be seen as a requirement for zero-trust networks, only a recommendation.

### Hardware-Based Zero Trust Supplicant
Given the limited options for deploying a TPM-based device authentication workflow, the most realistic option available is to use an authentication proxy alongside the device in question. This proxy should be deployed as close to the legacy component as possible, as it suffers from a number of the same attack vectors as legacy perimiter networks, given it is a perimiter itself.

## Inventory Management
Maintaining an inventory database is an important part of establishing the trust of clients that are connecting to your resources. It allows us to store metadata about the devices connected the network, driving the network policies they require. This needs to be a dynamic process, able to handle new workloads joining the network, both as new client devices are onboarded and as new virtualised and container workloads come online.

This dynamic process invovles new devices going through a **secure introduction** whereby it is introduced to the existing authorisation engine and assigned a degree of trust.

A good secure introduction system is
- single-use
- short-lived
- third-party

## Renewing Device Trust
Rotation of devices should be done for the same reason as the rotation of credentials - the longer a device has been running, the more suceptible it becomes to attack and compromise.

There are three scenarios we must consider when talking about rotating devices:
- virtual resources, like VMs
- physical datacentre resources
- client devices

With virtual resources, it's quite straightforward to recycle them if you are using configuration management or Infrastructure as Code tools.

With physical resources in the datacentre, the most practical way to rotate them, stopping short of replacing them kit entirely, is to reimage them.

Client devices are the most problematic scenario. A balance must be achieved between security of the network and convenience of the user. The longer we go between reimaging a client device, the more secure our endpoints must be to compensate.

In the event you are unable to reimage a device in a timely manner, or you want extra confirmation that the device is in a good state, two methods exist to verify the device.

**Local measurement** is where the device sends a report of its configuration to a remote endpoint. There are two ways of doing this: **hardware-backed** and **softeware-backed**. Hardware-backed can leverage the TPM and send a list of hashes to the remote endpoint for **remote-atestation**. The downside to this is it can only confirm low-level software configuration, and will miss that an attacker has started a process in user-space. For this, software-backed measurement can be employed such as installed a vulneability scanner on the device. The problem here is that an attacker who has gained elevated privileges can falsify the results sent back. Hardware-backed measurement is reliable but limited, while software-backed is vulnerable to exploitation, but can deliver a broad report on the state of the device.

The best solution is **remote measurement**. This is where a remote system inspects the devices itself, rather than relying on the device to report their own state. A compromised device can report anything it wants, so this mitigates that risk with separation of duties. It's the difference between asking someone if they robbed a bank and watching them do so. They are unlikely to confess to doing it, but if you catch them in the act it's hard to argue against it.

## Software Configuration Management
Existing configuration management systems can be used as an inventory management system because of the rich data they collect on the nodes under their control. This is useful for young zero trust networks since it lowers the barrier to entry by repurposing systems that are likely already in use.

It's important to bear in mind that all the information reported by the CM system is self-reported by the node, meaning a compromised node can misrepresent itself, so this information should be taken as a hint when making decisions, rather than the truth.

For validating the key attributes of the device, such as device type, role, ip address and public key, the provisioner is the trusted system that can verify those details. Since these attributes are the important ones that are used for making decisions, write access to these should be tightly controlled to prevent an attacker misrepresenting the device.

## Using Device Data for User Authorization
Since device authentication comes before user authentication, we can use knowledge of the device in the user authorization workflow. If a user is logging in from a device that is not found in the inventory management system, or has been but has also been issued to another employee, then this can result in a degraded trust score.

This is why it's important to maintain an accurate inventory of corporate devices.

## Trust Signals
There are a few signals that can be used to ascertain the trustworthiness of a device on the network.

- **Time Since Image** - the longer a device goes without being re-imaged, the higher the risk of it being compromised.
- **Historical access** - a device not seen in months suddenly accessing a resource is suspicious, as is a device that has made 1000 requests to a resource in the last hours despite only making 4 in the last month.
- **Location** - the absolute location of the device on the network shouldn't be used as a trust signal, rather the patterns in the location. A device that is logging on from Europe now after logging on from the US an hour before is suspicious.
- **Network Communication Patterns** - a node that has never used SSH before suddenly starting to make SSH connections to hosting providers in another continent is suspicious.