# Summary


# Notes
A network agent is the marriage of user and device to form a single idenitty. It prevent users from logging on from just any device, trusted or not. It must be from their personal trusted device. More generally, it is the combination of data know about the actor requesting the access. The agent is ephemeral, existing for the purpose of evaluating a policy, and forming a point-in-time identity.

Some of the data in the agent can be volatile. For example, the trust score needs to be changing enough that the system can respond to a trusted user begining to attack the system. Similarly, a device being bootstrapped might not have a user assigned to it yet, and this must be relfected in the authorisation workflow.

Examples of information an agent might contain are:
- agent trust score
- user trust score
- user roles or groups
- user place of residence
- user authentication method
- device trust score
- device manufacturer
- TPM manufacturer and version
- current device location
- ip address

## How Is An Agent Used
Authorisation decisions are made against the agent as a whole, not parts of it such as user or device.

An agent is formed only after user and device authentication, both of which might be authenticated differently (user with typical multifactor approach and the device with X.509). Typically authentication is session oriented while authorisation is request oriented.

## How To Expose An Agent
The agent will typically contain PII that would compromise the privacy of the user if it were ever leaked, so the entire lifecycle of the agent should be limited to the secure systems of the control plane.

Sometimes, parts of the agent need to be exposed to data plane systems for the purpose of implemented authorisation-based policies within the system, for example, to only allow a user to search a subset of data based on their authorisation.

## No Standards Exist
All zero trust networks have been build in-house so far, so there is no standardisation of the agent format. A standardised agent would allow for the mixing and matching of control plane components.

Standardisation of the agent is essential to the scaling of the control plane. New systems being introduced need to be confident of finding certain pieces of information at certain coordinates.

Data cleanliness is an issue that is unavoidable in assembling an agent. Many fields may be unpopulated for one reason or another, and since data cleanliness only gets hard with scale, it is best for policies to expect some fields to be absent. It is a useful thought experiment to think about what alternative pieces of data would suffice in its absence.

Zero trust networks are a new field and still under active development, so standardisation has not yet occured. When designing an agent it is recommended to make it as flexible and extensible as possible, prefering loose typing or no typing at all over strong typing.