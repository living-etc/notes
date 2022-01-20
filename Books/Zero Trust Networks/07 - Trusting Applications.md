# Summary


# Notes
There are several requirements for trusting the code running on a device:
- trusting the programmers who wrote the code
- trusting the build process that processes the code into a trust-worthy application
- trusting the deployment process that puts the code on the infrastructure to be run
- continual monitoring of the trusted applications for attempts to coerce them with malicious actions

## Understanding the Application Pipeline
The four steps in the development pipeline of an application can generally be broken down into four distrinct phases:
- source code
- build
- distribution
- execution

Each of these steps needs to be fully auditable with cryptographic validation.

## Trusting Source
A zero trust network will attempt to identify malicious developers rather than reduce the trust of the non-malicious ones.

### Securing the Repository
Securing the repository from which the build system reads the code is needed in order to secure the build pipeline, and this can be done using the traditional methods of least privilege, where a developer only has enough access to the code to complete the task at hand. Since code can enter the repository in a number of ways, however, securing the build source repository isn't usually enough.

### Authentic Code and the Audit Trail
Storing the source code in its repository using cryptographic techniques minimises the risk of malicious code being pushed into the history of the repository. Git makes it impossible for existing commits to be edited without any other contributor noticing.

It doesn't make it impossible for a trusted developer to first pull a malicious commit onto their computer before pushing it to the repository, not does it prevent a malicious actor changing the commit metadata to look as if it was written by a trusted developer.

Using GPG to sign each commit mitigates these problems. The repository can reject any push that includes unsigned commits, and the build system can refuse to build any version of the code that includes unsigned commits.

### Code Reviews
Signed contributions allows us to verify the author of the code, but not its correctness. Having a second set of eyes on each change reduce the risk of someone introducing vulnerabilities.

## Trusting Builds
In trusting the build system, we want to assert the following three things:
1. the source code it build is the code we intended to build
2. the build process/configuration is what which we intended
3. the build itself was performed faithfully, without manipulation

### The Risk
A build system can ingest signed code, and produce a signed application, but the build process in between is difficult to verify. Is the signed output containing the exact code we intended to build or was other malicious code compiled in?

Outsourcing this to a hosted service should be done with care. You need to be able to trust the security posture of that vendor, and compare that posture to your chance of being a high value target.

Trust in the build servers should be considered apart from trust in the build configuration. Changes in the configuration are easier to make than compromising the servers the build is running on.

### Trusted Input, Trusted Output
the build system needs to verify the authenticity of the source from which it is pulling the code, commonly using TLS. It should also validate the signature on each commit and tag before starting a build.

To mitigate the risk of malicious configuration causing a malicious output to be produced, the configuration for the build process should also be stored in source control with changes signed and verified in the same way as those of the source code being built.

Finally, the build system should sign the output it produces so the downstream systems can verify the artifact they are consuming. The signed output and a cryptographic hash of the that output should be generated and secured before being distributed downstream.

### Reproducible Builds
Reproducible builds are the best way of guarding against subversion of the build pipeline. The code used to create the build pipeline is build and compiled into a cryptographically verifiable binary in the same way as the output of the build system. This binary can be used to produce the same artifact bit-for-bit on a developers local machine, as well as reproducing the same output in the build pipeline.

Isolating the build from the host running the build is a useful way of achieving this. historically, chroot jails have been used, and more recently contaimers or VMs have been used.

### Decoupling Release and Artifact Versions
Immutable builds are important to protecting the trust placed in an artifact. By ensuring that a known good artifact cannot be overwritten with the same version number, we guarantee that redistribution of this artifact will not introduce any malicious code.

Separating the release and build versions from one another is critical for this. If, for example, we produce a particular artifact with a particular version, but discovered a fault in the build system that required us to produce a new artifact without change the application source code, how would the version numbers be handled?

The most desirable situation is to increase the version number to refect the state of reality - two artifacts have been build, so two different version numbers are required.

By building our artifacts with a version (eg timestamp) separate from the release (eg semvar) we can maintain separate stories for the artifact (internal to the team) and for the release (external to the customers) and the distribution system can be responsible for mapping an artifact version to a release version.

## Trusting Distribution
The act of promoting an artifact to be delivered downstream to customers is called Distribution. Since not all artifacts are built to be distributed, there must be trust in the choice of artifacts that are chosen for distribution.

### Promoting an Artifact
The act of asigning a release version to an artifact described prviously should be immutable, so once the release is created, it cannot be changed, not should the artifact be changed in the act of adding this release number.

### Distribution Security
The artifact must be secured during distribution from the build system to the consumers, and the consumers must be able to verify this.

### Integrity and Authenticity
The integrity of a software package is typically verified through hashing, which is the process of generating a cryptographic hash of the released binary. The authenticity of the release can be verified through signing, where the author signs it with their private key and distributes the public key. The public key can be used to verify the software has not been tempered with in transit.

For example, APT repositories include a `Packages` file that contains a checksum of each package, allowing us to verify the integrity of the packages being installed. This only protects against corruption, since an attacker can just generate a new checksum.

This is solves by the `Release` file, which contains a checksum of the Packages file. This file is encrypted using the private key of the publisher. Being able to decrypt this file verifies that the original publisher was the one to distributed it, and not an intermediate attacker. This then verifies the integrity of the contents of the Release file, which in turn verifies the integrity of the Packages file.

### Trusting a Distribution Network
Unless all packages are being downloaded from the build server, there will be a distribution network of intermediate servers, usually called mirrors, serving up the packages.

These mirrors must be trusted by the consumer of the packages. It is not enough to use the signing and hasing methods described above. One form of attack that can come from connecting to a malicious mirror is a package can be downgraded, reintroducing security vulnerabilities that were preivously fixed. The vulnerbale package will still pass the signing and hashing tests since it is still legitimate.

Client should be connecting to TLS-protected package repositories. This ensure they are connecting to a trusted repository, and that no tampering of the package can occur in transit.

## Humans in the Loop
Some human intervention in the release process is ideal for minimising the risk of release automation being exploited to release a malicious artifact. For example, one or more people might be required to copy an artifact from the build database to the release database. Another example is having multiple people involve in a code signing ceremony, where multiple approvals are required to unlock the HSM holding the private key.

## Trusting an Instance
In order to understand what to expect from your network you need to understand what is running on it.

### Upgrade-Only Policy
A common attack is to force a downgrade of a software package installed on an instance to one with an exploitable vulnerbaility. This can be mitigated by enforcing an upgrade-only policy on the instances. If the package mirror is under our control, another option is to only server the latest version of the package and block access to other mirrors where a vulnerable package might be obtained.

### Authorized Instances
Authorising the instances we have running is an important way of preventing rogue instances that have either falled out of the deployment system or have been launched by an attacker. Authorising them with a network policy as described in Chapter 4 wouldn't be sufficient, since those policies host/device specific whereas we want something application-specific.

Applications can be authorised using the secrets they need. All applications need some sort of secret in order to do their job, whether it's a certificate, api key, etc, and they will retrieve them on startup. By generating these secrets on demand as the instances are launched, and setting a time limit on them, we can assert on exactly what is running in our estate since we know the number of secerts we generated. who we generated them for and how long they last.

#### Trusted third parties in instance authorization
Rather than giving the deployment service direct access to secrets, we can instead of a secret manager holding the secrets as a trusted third party. The deployment service can notify the secret manager of the impending deployment, authorising the creation and release of new secrets. The instances can then retrieve these secrets after they launch.

![[Trusted third parties in instance authorization.png]]

## Runtime Security


### Secure Coding Practices
It's not feasible to act reactively to security vulnerabilities, only fixing them after they have been discovered. Developers need to be proactive in preventing them in the first place using secure coding techniques. This can take the form of using libraries that don't trust user input and sanitise it before acting on it, such as in database queries.

### Isolation
Applications running in shared environments are at risk of an attacker being able to move leterally between them. This risk can be mitigated by running applications in isolated execution environments, examples of which include:
- SELinux, AppArmour
- BSD Jails
- Virtualisation
- Containerisation
- Apple's App Sandbox
- Windows Isolated Applications

Isolation techniques generally break down into two categories: **virtualisation** and **shared kernal** environments. Virtualisation is generally the more secure since the application is contained within a virtual hardware environment, whereas shared kernal environments have the applications shared the same hardware, with some isolation imposed by the kernel.

### Active Monitoring
Aside from the standard monitoring the happens in a production system, there is a need for active security monitoring. this differs from the logging of security events for review in that it actively scans the running applications for vulnerabilities, similar to the ones that might be run in a build pipeline.

Some types of active security scanning are:
- Fuzzing
- Injection Scanning
- Network Port Scanning
- CVE Scanning

Response to findings from these can either be to alert a human to take action, or for the system to actively respond to it, such as by revoking credentials or isolating an instance for forensics.