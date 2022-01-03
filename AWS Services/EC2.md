---

---

```ad-abstract
Amazon EC2 is the reason you can get your application onto a server in 10 minutes with nothing more than a credit card. It provides a choice of nearly 400 instance for various types of workloads including: memory, CPU network optimization; GPU workloads; and a choice of Linux flavors as well as Windows and macOS. You can configure the CPU and memory of your instance for your workload, paying based on your choice, and you can terminate it at any time, instantly stopping spending money.
```

- [[#Common components of an EC2 instance|Common components of an EC2 instance]]
- [[#Reservations|Reservations]]
- [[#Amazon Machine Images|Amazon Machine Images]]
	- [[#Launch Permissions|Launch Permissions]]
	- [[#How You're Charged|How You're Charged]]
	- [[#Linux AMI virtualization types|Linux AMI virtualization types]]
	- [[#Boot Modes|Boot Modes]]
- [[#Instances|Instances]]
	- [[#Start, stop and terminate instances|Start, stop and terminate instances]]
	- [[#Instance Types|Instance Types]]
		- [[#Overview|Overview]]
		- [[#Hardware Specifications|Hardware Specifications]]
		- [[#Processor Features|Processor Features]]
		- [[#AMI Virtualisation Types|AMI Virtualisation Types]]
		- [[#Instance Built on the Nitro System|Instance Built on the Nitro System]]
		- [[#Network and Storage Features|Network and Storage Features]]
		- [[#Networking Features|Networking Features]]
		- [[#Storage Features|Storage Features]]
		- [[#Instance limits|Instance limits]]
		- [[#General Purpose|General Purpose]]
		- [[#Instance Performance|Instance Performance]]
		- [[#Network Performance|Network Performance]]
		- [[#SSD I/O Performance|SSD I/O Performance]]
		- [[#Instance Features|Instance Features]]
		- [[#Burstable Performance Instances|Burstable Performance Instances]]
	- [[#Instance Purchasing Options|Instance Purchasing Options]]
		- [[#On-Demand Instances|On-Demand Instances]]
		- [[#Reserved Instances|Reserved Instances]]
			- [[#Commitment Term|Commitment Term]]
			- [[#Payment Options|Payment Options]]
			- [[#Offering Classes|Offering Classes]]
			- [[#Scope|Scope]]
			- [[#Normalization Factor|Normalization Factor]]
			- [[#Usage Billing|Usage Billing]]
			- [[#Consolidated Billing|Consolidated Billing]]
			- [[#Modifying Reserved Instances|Modifying Reserved Instances]]
			- [[#Exchange Reserved Instances|Exchange Reserved Instances]]
		- [[#Spot Instances|Spot Instances]]
			- [[#Best Practices|Best Practices]]
			- [[#How Spot Instances Work|How Spot Instances Work]]
			- [[#Savings from purchasing Spot Instances|Savings from purchasing Spot Instances]]
			- [[#Spot Instance Requests|Spot Instance Requests]]
			- [[#Rebalance recommendations|Rebalance recommendations]]
			- [[#Spot Instance Interruptions|Spot Instance Interruptions]]
			- [[#Billing for interrupted spot instances|Billing for interrupted spot instances]]
			- [[#Spot Instance data feed|Spot Instance data feed]]
			- [[#Spot Instance limits|Spot Instance limits]]
			- [[#Burstable Performance Instances|Burstable Performance Instances]]
		- [[#Dedicated Hosts|Dedicated Hosts]]
		- [[#Dedicated Instances|Dedicated Instances]]
		- [[#On-Demand Capacity Reservations|On-Demand Capacity Reservations]]
- [[#Fleets|Fleets]]
	- [[#EC2 Fleets|EC2 Fleets]]
	- [[#Spot Fleets|Spot Fleets]]
	- [[#Monitor Fleet Events|Monitor Fleet Events]]
- [[#Monitor|Monitor]]
	- [[#Monitoring with CloudWatch|Monitoring with CloudWatch]]
- [[#Networking|Networking]]
- [[#Security|Security]]
- [[#Storage|Storage]]
- [[#Resource and tags|Resource and tags]]
- [[#Troubleshoot|Troubleshoot]]





# Common components of an EC2 instance
-   Instance, the VM
-   EBS volume for persistent storage
-   SSH Keypair for secure access
-   Security group
-   AMI
-   Instance type with configurable CPU, memory, storage and network throughput
-   Elastic IP Address for public access
-   Tags
-   Multiple physical locations for instances and EBS volumes, called **Regions** and **Availability Zones**
-   An IAM role defining the permissions the instance has
-   Platform, the operating system running in the instance
-   Tenancy

# Reservations
```ad-note
Describes a launch request for one or more instances, and includes owner, requester, and security group information that applies to all instances in the launch request.
```

-   [Docs](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_Reservation.html)
-   AWS EC2 what is a reservation ID exactly and what does it represent? \[[Serverfault](https://serverfault.com/questions/749118/aws-ec2-what-is-a-reservation-id-exactly-and-what-does-it-represent)\]
# Amazon Machine Images
An AMI contains the following:
-   a snapshot of an EBS volume for EBS-backed instances, or a template for the root volume for instance-store-backed instances
-   launch permissions that define which AWS accounts have permission to launch instances with the AMI
-   A block device mapping that specific the volumes to attach to the instance at startup

## Launch Permissions

-   public: the owner grants launch permissions to all AWS accounts
-   explicit: the owner grants launch permissions to specific AWS accounts
-   implicit: the owner has implicit launch permissions for an AMI

## How You're Charged

> With AMIs backed by instance store, you're charged for instance usage and storing your AMI in Amazon S3. With AMIs backed by Amazon EBS, you're charged for instance usage, EBS volume storage and usage, and storing your AMI as an EBS snapshot.

## Linux AMI virtualization types

Paravirtual (PV) AMIs include special drivers for greater network and disk I/O performance. It is an older virtualization type that has been superseded by Hardware Virtual Machine (HVM) AMIs which now have PV drivers available to them. As such, latest generation instance types do not support PV AMIs.

## Boot Modes

AMIs have an optional launch boot mode parameter which overrides the default boot mode defined by the CPU family of the instance type - **UEFI** for Graviton instance types and **legacy-bios** for Intel and AMD.

In order for an instance to launch successfully with the specified boot mode, the operating system must be configured to support it. This configuration is different for different operating systems.

Intel and AMD instances can only launch with UEFI on latest generation instance types.

UEFI Secure Boot is currently not supported.
# Instances
## Start, stop and terminate instances

You can **stop** an EBS-backed instance and all of its EBS volumes remain attached. You can restart the instance any time, and you will not be charged when the instance is stopped.

While stopped, you can change the instance type, kernel and RAM disk.

You cannot stop and instance-store-backed instance.

You can **terminate** an instance to shut it down. When this happens, its root volume is deleted, but any other attached EBS volumes are preserved by default, unless the volumes `deleteOnTermination` attribute specifies otherwise.

You can prevent accidental termination of an instance by setting its `disableApiTermination` attribute to `true`, and you can control the shutdown behavior of the operating system (`shutdown -h` on Linux and `shutdown` on Windows) by setting the `instanceInitiatedShutdownBehavior` attribute to `stop` or `terminate` as desired.

## Instance Types

### Overview

Each instance type is grouped onto a particular host computer based on its instance family. For example, all T-type instance will be grouped onto computers dedicated to that type.

While some resources of the host computer are dedicated to the guest instances (such as CPU and Memory) other resources, such as network and disk subsystem, are shared. When these shared resources are underused, an instance can use a high share than if all instance were trying to use it at once, in which case they would each receive an equal share.

### Hardware Specifications

EC2 instance typically run on 64-bit virtual Intel CPUs.

### Processor Features

Amazon EC2 instances that run on Intel processors may include the following features. Not all of the following processor features are supported by all instance types.

**Intel AES New Instructions (AES-NI)** - Intel AES-NI encryption instruction set improves upon the original Advanced Encryption Standard (AES) algorithm to provide faster data protection and greater security. All current generation EC2 instances support this processor feature.

**Intel Advanced Vector Extensions (Intel AVX, Intel AVX2, and Intel AVX-512)** - Intel AVX and Intel AVX2 are 256-bit, and Intel AVX-512 is a 512-bit instruction set extension designed for applications that are Floating Point (FP) intensive. Intel AVX instructions improve performance for applications like image and audio/video processing, scientific simulations, financial analytics, and 3D modeling and analysis. These features are only available on instances launched with HVM AMIs.

**Intel Turbo Boost Technology** - Intel Turbo Boost Technology processors automatically run cores faster than the base operating frequency.

**Intel Deep Learning Boost (Intel DL Boost)** - Accelerates AI deep learning use cases. The 2nd Gen Intel Xeon Scalable processors extend Intel AVX-512 with a new Vector Neural Network Instruction (VNNI/INT8) that significantly increases deep learning inference performance over previous generation Intel Xeon Scalable processors (with FP32) for image recognition/segmentation, object detection, speech recognition, language translation, recommendation systems, reinforcement learning, and more. VNNI may not be compatible with all Linux distributions.

The following instances support VNNI: `M5n`, `R5n`, `M5dn`, `M5zn`, `R5b`, `R5dn`, `D3`, and `D3en`. `C5` and `C5d` instances support VNNI for only `12xlarge`, `24xlarge`, and `metal` instances.

### AMI Virtualisation Types

Current generation instance support the Hardware Virtual Machine (HVM) type only. Some older instance types support Paravirtual (PV), but only in specific regions.

### Instance Built on the Nitro System

Nitro is a collection of AWS-built hardware and software components that increase performance of guest instance by reducing the virtualization overhead

The following components are part of the Nitro System:

-   Nitro card
    -   Local NVMe storage volumes
    -   Networking hardware support
    -   Management
    -   Monitoring
    -   Security
-   Nitro security chip, integrated into the motherboard
-   Nitro hypervisor - A lightweight hypervisor that manages memory and CPU allocation and delivers performance that is indistinguishable from bare metal for most workloads.

### Network and Storage Features

The network and storage features of an instance are determined by the instance type.

### Networking Features

-   IPv6 is supported on all current generation instance types
-   Placement groups are available for supported instance types to affect the distribution of instance where network latency between instances is important.
-   Enhanced networking is available on all current generation instance types with the following benefits:
    -   Traffic within the same region using IPv4 or IPv6 can support up to 5Gbps for single-flow traffic and up to 25Gbps for multi-flow.
    -   Traffic to and from S3 buckets within the same region over public IP address space or through a VPC endpoint can use all available instance aggregate bandwidth.
-   The maximum transmission unit (MTU) varies across instance types.
    -   All instance types support standard Ethernet V2 1500 MTU.
    -   All current generation instance types support 9001 MTU, or jumbo frames.

### Storage Features

-   All instance types support EBS volumes, while only some support instance store volumes.
-   You can gain additional EBS I/O by using EBS-optimised instances. Some instance types are EBS-optimised by default.

### Instance limits

The limit on the number of a particular instance type an account can run is based on the number of vCPUs in use.

vCPU limits only apply to on-demand and spot instances, not to reserved instances.

### General Purpose

General purpose instance types provide a balance of compute, memory and networking resources, and can be effectively utilised for a wide range of workloads.

The following general purpose instance types are available:

-   **M5 and M5a** - standard general purpose instances running on Intel Xeon® Platinum 8175M processors (M5) or AMD EPYC 7000 series processors (M5a) CPUs. Ideal for: small and midsize databases; data processing tasks that require additional memory; caching fleets; and backend servers for enterprise applications such as SAP, Sharepoint, etc.
-   **M5zn** - ideal for applications that benefit from _extremely high single-thread performance_ to _high throughput, low latency networking_. Well suited for gaming, high-performance computing and simulation modeling.
    -   powered by Intel Xeon Scalable processors in the cloud, with an all-core turbo frequency up to 4.5 GHz.
-   **M6g and M6gd** - balanced compute, memory and networking resources running on AWS Graviton2 CPUs, ideal for a broad range of general purpose workloads, including application servers, microservices, gaming servers, midsize data stores and caching fleets. M6gd instances have local NVMe-based SSDs physically connected to the host server, as opposed to the M6g instances that use EBS.
-   **m6g.metal** - bare metal version of the M6g.
-   **M6i** - higher-spec-ed Intel Xeon CPUs than M5
    -   highest maximum network speed of the general purpose instance types, at up to 50 Gbps
    -   powered by 3rd generation Intel Xeon Scalable processors (code named Ice Lake).
    -   Support for new Intel Advanced Vector Extensions (AVX 512) instructions for faster execution of cryptographic algorithms.
    -   Support for always-on memory encryption using Intel Total Memory Encryption (TME).
-   **Mac1** - powered by Apple Mac Mini computers.
-   **T2, T3, T3a and T4g** - burstable performance instance types. T2 is last generation, T3 current generation Intel CPU, T3a current generation AMD CPU and T4g current generation Graviton2 CPU. Ideal for intermittent workloads, such as:
    -   websites and web applications
    -   code repositories
    -   non-production test environments
    -   microservices

### Instance Performance

Some instance types allow you to control the C-states and P-states of the CPU.

EBS-optimised instances improve the performance of traffic to and from EBS.

### Network Performance

You can enable enhanced networking on supported instance types to provide lower latencies, lower network jitter, and higher packet-per-second (PPS) performance. Most applications do not consistently need a high level of network performance, but can benefit from access to increased bandwidth when they send or receive data.

Some instance types have a baseline network IO which they can burst above with a network I/O credit mechanism, similar to the burstable CPU instance types.

### SSD I/O Performance

### Instance Features

### Burstable Performance Instances


## Instance Purchasing Options

### On-Demand Instances

With On-Demand Instances, you pay for compute capacity by the second with no long-term commitments. You have full control over its lifecycle—you decide when to launch, stop, hibernate, start, reboot, or terminate it.

It's recommended to use On-Demand Instances for applications with short-term, irregular workloads that cannot be interrupted.

### Reserved Instances

Reserved instance (RIs) aren't physical instances, rather a discount applied to the AWS bill tot he use of on-demand instances. These instances must match certain attributes, instance type and region.

If you already have on-demand instance running that match the attributes of your reserved instances, then the RIs will be immediately applied to those running instances. This applies to both zonal and regional RIs.

#### Commitment Term

The term commitment, which isn't automatically renewed, is either:

-   **One-year**: A year is defined as 31536000 seconds (365 days).
-   **Three-year**: Three years is defined as 94608000 seconds (1095 days).

#### Payment Options

RIs have the following payment options:

-   **All Upfront** - full payment is made at the start of the term
-   **Partial Upfront** - a portion of the total cost is paid upfront, and the remainder paid through a discounted hourly rate. You'll pay this regardless of whether or not the instances are being used.
-   **No Upfront** - you are billed the discounted hourly rate for the duration of the commitment period, regardless of whether the instance is being used.

Reserved instance to do automatically renew. To ensure uninterrupted savings, you can queue a RI purchase up to three years in advance.

#### Offering Classes

There are two offering classes of RIs:

-   **Standard** - provide the largest discount, but can only be modified, not exchanged. These RIs can be bought and sold in the RI marketplace but can't be exchanged for other RIs.
-   **Convertible** - provides a lower discount, but can be exchanged for another convertible RI with different attribute. Can also be modified. These can't be bought or sold in the RI marketplace.

#### Scope

RIs have two possible scopes - **regional**, where the RI is scopes to a particular region, and **zonal**, where the RI is scopes to a single availability zone. They have the following differences:

-   **capacity reservation** - a regional RI does not reserve capacity, whereas a zonal RI does reserve capacity in the specified AZ.
-   **AZ flexibility** - a regional RI can be launched in any AZ in the region, whereas a zonal RI is tied to the AZ in which it's been purchased.
-   **instance size flexibility** - regional RIs can be used on any size of instance within the instance family, whereas a zonal RI is purchased for a specified size and type of instance
-   **queuing a purchase** - purchases for regional RIs can be queued, whereas purchases for zonal RIs can't.

#### Normalization Factor
Each instance has an **instance size footprint** that determines its normalization factor.

Some examples of instance normalization factors can be seen in this table.

| Instance Size | Normalization Factor |
| ------------- | -------------------- |
| nano          | 0.25                 |
| micro         | 0.5                  |
| small         | 1                    |
| medium        | 2                    |
| large         | 4                    |

What this means is if you purchase a `t3.medium` RI, and have two `t3.small` on-demand instances running, then the discount will be applied to both instances in full.

On the other hand, if your instead have a single `large` on-demand instance running, the discount will only be applied to half the cost of the instance.

Normalization factors also exist for bare metal instances, but does not scale in the same way as for normal instances. For example, an `i3.metal` instance has the same normalization factor of `128` as a `i3.16xlarge`.

#### Usage Billing

RIs are generally charged per clock-hour during the commitment period. Per-second billing is available for instances running an open source Linux distribution, whereas per-hour billing is used for commercial distributions.

A single RI billing benefit can only apply to a total of 3600 seconds (one hour) of instance usage per clock hour. These second can be allocated to a single instance, or spread across multiple instances.

If you purchase a single `t3.medium` RI and run four `t3.medium` instances for an hour, one instance will receive the RI discount while the other three are charged at the on-demand price. On the other hand, if you run your four `t3.medium` instances for fifteen minutes each, they all receive the RI discount and no on-demand charge will be applied.

#### Consolidated Billing

RIs that are purchased in an account that is the payer account for a set of consolidated billing accounts, then the benefits of those RIs are shared across all the members accounts.

#### Modifying Reserved Instances

You can modify the following attributes of your standard and convertible RIs:

-   AZ within a region
-   Scope, from regional to zonal and vice versa
-   Instance size within the same instance family
-   Network, from EC2-classic to VPC and vice versa (only available in accounts created after 2013-12-04)

You can allocate your reservation into different instance sizes across an instance family, provided the instance size footprint is unchanged after the modification.

#### Exchange Reserved Instances

> Only available on convertible reserved instances. Not available on standard.

A convertible RI can be exchanged for another convertible RI with different attributes, including instance family, platform (operating system) and tenancy.

The following rules apply:

-   convertible RIs can only be exchanged for other convertible RIs
-   you cannot exchange for RIs in another region
-   you can receive only one new convertible RI at a time, but can exchange one or more RI.
-   you can modify a reservation and trade part of the split
-   you can mix and match some payment options, but not all
    -   All Upfront can be exchanged for Partial Upfront, and vice versa
    -   No Upfront can be exchanged for All or Partial Upfront
    -   No Upfront can be exchanged for another No Upfront
    -   You cannot exchange All or Partial Upfront for No Upfront, provided the new instances hourly price is at least as high as the old one
    -   if you exchange multiple RIs, the expiration date of the new RI is the one furthest in the future
    -   if you exchange a single RI, it must have the same term as the new one
    -   if you merge multiple RIs with different lengths, the new RI has a 3 year term.

### Spot Instances

> A Spot Instance is an instance that uses spare EC2 capacity that is available for up to 90% less than the on-demand price.

Concepts:

-   **Spot capacity pool** - a set of unused EC2 instance with the same instance type (`t3.medium`) in the same AZ
-   **Spot price** - the current price of a spot instance per hour
-   **Spot instance request** -
-   **EC2 instance rebalance recommendation** - a notice emitted by EC2 that an instance is at increased risk of being interrupted due to reduced spot capacity being available.
-   **Spot instance interruption** - a two-minute notice emitted by EC2 when spot capacity has been exhausted and an instance is going to be interrupted.

#### Best Practices

> Spot Instances are recommended for stateless, fault-tolerant, flexible applications. Spot Instances are not suitable for workloads that are inflexible, stateful, fault-intolerant, or tightly coupled between instance nodes.

-   **Prepare individual instance for interruptions**. Create a rule in amazon EventBridge to capture the two possible signals - rebalance recommendation and instance interruption.
-   **Be flexible about instance types and Availability Zones**. This give spot a better chance at finding available capacity. A good rule of thumb is to be flexible across at least 10 instance types for each workload. In addition, make sure that all Availability Zones are configured for use in your VPC and selected for your workload.
-   **Use EC2 Auto Scaling groups or Spot Fleet to manage your aggregate capacity**. Prefer to think in terms of resources such as vCPU and memory capacity, rather than instance capacity. This way you can use Auto Scaling groups and Spot Fleets to maintain versatile fleets of compute resource.
-   **Use the capacity optimized allocation strategy**. Use the `capacity optimised` allocation strategy of your auto scaling group to provision spot instances on servers that have the most spare capacity. This decreased the change of your instance being reclaimed.
-   **Use proactive capacity rebalancing**. Capacity Rebalancing helps you maintain workload availability by proactively augmenting your fleet with a new Spot Instance before a running Spot Instance receives the two-minute Spot Instance interruption notice.
-   **Combine multiple of the above**. Combine the `Capacity Rebalancing` feature of the Spot Fleet with the `capacity optimised` allocation strategy of the auto scaling group as well mixed instance policy to maximize the flexibility of your capacity across multiple availability zones.

#### How Spot Instances Work

-   **Launch Spot Instances in a launch group**. You can request to have a number of spot instances launched into a single group together. The request will only succeed if the entire group can be launched at once, and if the spot price exceeds your maximum, they will all be terminated at once. This is inflexible and reduces the likelihood of your request succeeding.
-   **Launch Spot Instances in an Availability Zone group**. Same as the launch group, but where the spot instances are launched into an availability zone. Same limitations as well. The instances will be launched in the AZ for the specified subnet.
-   **Launch Spot Instances in a VPC**. Specify a subnet into which the spot instances are launched.

#### Savings from purchasing Spot Instances
![[spot-savings.png]]

**To view the savings information for a Spot Fleet (console)**

1.  Open the Amazon EC2 console at [](https://console.aws.amazon.com/ec2/)[https://console.aws.amazon.com/ec2/](https://console.aws.amazon.com/ec2/).
2.  On the navigation pane, choose **Spot Requests**.
3.  Select the ID of a Spot Fleet request and scroll to the **Savings** section. Alternatively, select the check box next to the Spot Fleet request ID and choose the **Savings** tab.

#### Spot Instance Requests
![[spot_lifecycle.png]]

You can request to launch dedicated spot instances, either by specifying the tenancy in the spot request, or by requesting to launch into a VPC with an instance tenancy of `dedicated`.

#### Rebalance recommendations
An EC2 instance rebalance recommendation is a signal emitted when a spot instance is at increased risk of interruption. The signal can arrive sooner than the two-minute spot-instance interruption signal, giving you more time to respond to the lack of available capacity, though rapid changes in capacity mean the rebalance recommendation signal can arrive alongside the interruption signal.

There are two ways this signal can be handler: Amazon EventBridge and instance metadata.

Using EventBridge, a signal looking like this will arrive:

```
{
	"version": "0",
	"id": "12345678-1234-1234-1234-123456789012",
	"detail-type": "EC2 Instance Rebalance Recommendation",
	"source": "aws.ec2",
	"account": "123456789012",
	"time": "yyyy-mm-ddThh:mm:ssZ",
	"region": "us-east-2",
	"resources": [
		"arn:aws:ec2:us-east-2:123456789012:instance/i-1234567890abcdef0"
	],
	"detail": {
		"instance-id": "i-1234567890abcdef0",
		"instance-action": "terminate"
	}
}

```

You can create an EventBridge rule to handle it.

Using instance metadata, you can retrieve the signal with the following command:

```
curl <http://169.254.169.254/latest/meta-data/events/recommendations/rebalance>

```

If the signal has been emitted, you will see the following result

```
{"noticeTime": "2020-10-27T08:22:00Z"}

```

or else you will get a 404 back.

#### Spot Instance Interruptions

A spot instance can be terminated for the following reasons:
-   **price** - the spot price is greater than your maximum price
-   **capacity** - if available capacity drops too far, EC2 will reclaim running spot instances to fulfill on-demand capacity requirements
-   **constraints** - constraints in the spot request, such as AZ or launch group, are no longer met.

You can specify what EC2 does when it interrupts a spot instance:
-   **stop** the interrupted instance
-   **hibernate** the interrupted instance
-   **terminate** the interrupted instance (default)

To **stop** a spot instance, the following prerequisites must be met:
-   **Spot instance request type** - must be `persistent`, and musn't specify a launch group.
-   **EC2 Fleet or Spot Fleet request type** - must be `maintain`.
-   **Root volume type** - must be EBS.

Once a spot instance has been stopped by the spot service, only the spot service can restart it when capacity becomes available to satisfy the spot instance request.

To **hibernate** a spot instance, the following prerequisites must be met:
-   **Spot instance request type** - must be `persistent`, and musn't specify a launch group.
-   **EC2 Fleet or Spot Fleet request type** - must be `maintain`.
-   **Supported instance families** – C3, C4, C5, M4, M5, R3, R4
-   **Instance RAM size** – must be less than 100 GB
-   **Supported operating system** - you must install the hibernation agent, or use an AMI that already has it.
-   **Root volume type** - must be EBS, and must be large enough to store the instance memory

When a spot instance is hibernated, its EBS volume and private IP addresses are preserved and instance memory is saved to the volume. If there is not enough space on the volume, the hibernation agent isn't installed, or the OS doesn't support hibernation, hibernation fails and the instance is sopped instead.

**Spot instance bests practices**
-   Use the default maximum price, which is the On-Demand price.
-   Store important data regularly in a place that isn't affected by instance interruption
-   Divide the work into small tasks (using a Grid, Hadoop, or queue-based architecture) or use checkpoints so that you can save your work frequently.
-   Use the rebalance recommendation and two-minute interruption signals to manage the capacity of your spot instances.
-   Remember it is possible for the spot instance to be interrupted before the two-minute signal is received.
-   It is recommended to check for interruption notices every five seconds.

To prepare a spot instance for hibernation:
-   Install the hibernation agent
    -   Amazon Linux: `sudo yum install hibagent`
    -   Ubuntu: `sudo apt-get install hibagent`
-   Add the following to user data

```
#!/bin/bash
/usr/bin/enable-ec2-spot-hibernation

```

> Spot instance that are configured to hibernate on interruption, rather than stop, will not receive the interruption signal two minutes prior to interruption. Instead, hibernation will happen immediately, and only instance intended to be stopped will have two minutes to react.

If the instance is to be stopped or terminated, the time of this action can be found in the instance metadata:

```
TOKEN=`curl -X PUT "<http://169.254.169.254/latest/api/token>" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \\\\
curl -H "X-aws-ec2-metadata-token: $TOKEN" –v <http://169.254.169.254/latest/meta-data/spot/instance-action>

{"action": "stop", "time": "2017-09-18T08:22:00Z"}

```

You can look for `BidEvictedEvent` events in CloudTrail to see if Amazon EC2 interrupted a spot instance.

#### Billing for interrupted spot instances
| Who interrupts the Spot Instance                            | Operating system                            | Interrupted in the first hour                             | Interrupted in any hour after the first hour                                               |
| ----------------------------------------------------------- | ------------------------------------------- | --------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| If you stop or terminate the Spot Instance                  | Windows and Linux (excluding RHEL and SUSE) | Charged for the seconds used                              | Charged for the seconds used                                                               |
|                                                             | RHEL and SUSE                               | Charged for the full hour even if you used a partial hour | "Charged for the full hours used and charged a full hour for the interrupted partial hour" |
| If the Amazon EC2 Spot service interrupts the Spot Instance | Windows and Linux (excluding RHEL and SUSE) | No charge                                                 | Charged for the seconds used                                                               |
|                                                             | RHEL and SUSE                               | No charge                                                 | "Charged for the full hours used but no charge for the interrupted partial hour"           |

#### Spot Instance data feed

The spot instance data feed is a file delivered to an S3 bucket ever hour describing the cost usage of your spot instances for the previous hour. The files are gzipped, and can be split into multiple files is the usage is high enough.

#### Spot Instance limits

Spot instances are limited by the number of vCPUs being utilized by either running or requested spot instances. That means if you terminate your spot instances, but don't cancel the spot request, the request will still contribute towards the spot instance limit.

Current spot limits can be found [here](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-limits.html).

#### Burstable Performance Instances

### Dedicated Hosts

### Dedicated Instances

### On-Demand Capacity Reservations
# Fleets
A fleet allows you to launch multiple instances with a single API call. A fleet can contain multiple instance types and purchasing options and can be launched across multiple availability zones.

There is no additional cost for using EC2 fleet. You pay only for the EC2 instances.

## EC2 Fleets

EC2 fleets are a way of launching multiple instances with a single API call. When you launch a fleet, you specify your maximum capacity and budget, and EC2 will launch instances until one of those limits has been reached.

When specifying the desired capacity of the fleet, the default is number of instances, but you can also specify vCPUs, memory, storage or throughput.

Both spot and on-demand instances can be launched in the same fleet, with different capacities for each. If you have reserved instances for an instance type in your fleet, they will be used when you launch the on-demand instances.

Multiple instance types can be specified, with weighted capacities specified for each.

Fleets isn't available through the AWS console. It's only available through the CLI and API. The fleet request is limited to a single region, and to a single subnet in an availability zone.

> Aside: this can be combined with a placement group to deploy instances that need to talk to each other while processing their workload.

![[ec2-fleet.png]]

There are three fleet request types

-   **instant** - EC2 Fleet places a synchronous one-time request for the desired capacity and returns the instances launched in the API response.
-   **request** - EC2 Fleet places a synchronous one-time request for the desired capacity and does not attempt to replenish capacity by replacing interrupted spot instances.
-   **maintain** - this is the default, where EC2 Fleet places an asynchronous request for the desired capacity, and maintains capacity by replacing any interrupted spot instances.

The **instant** and **request** types will make only one attempt to launch the desired capacity. Any instances that fail to launch will not be retried.

Amazon EC2 Auto Scaling and Amazon EMR are built around EC2 Fleet, both using an **instant** request type.

An EC2 Fleet has the following allocation strategies for spot instances:

-   **lowest-price** - this is the default, where the spot instance comes from the capacity pool with the lowest price. Optionally specify **InstancePoolsToUseCount** to launch spot instances over a particular number of capacity pools to diversify you
-   **diversified** - the spot instances are distributed across all capacity pools.
-   **capacity-optimized** - the spot instances are launched into the pool with the highest capacity available. You can optionally specify a priority for each instance type in the fleet using **capacity-optimized-prioritized**.

On Demand capacity reservations can be used to fulfill fleet requests only if the request type is instant.

EC2 Fleets of type **maintain** can be configured for Capacity Rebalancing, where they automatically respond to rebalance recommendation signals emitted when a spot instance is at an elevated risk of being interrupted. This needs to be configured in the `MaintenanceStrategies` structure of the fleet request. This can only be configured when the fleet is created. Once the fleet is running, it cannot be changed. Note that the instance marked for rebalance is not automatically terminated.

> EC2 Fleet can launch new spot instances until the current capacity is double the target capacity. This allows a fleet composed entirely of spot instances to all be marked for rebalance, and to have their replacements launched automatically. It does, however, prevent any of those replacements being replaced until the old instances are terminated.

Spot instance marked for rebalance are not counted towards the desired capacity during scale-in or scale-out. Instance marked for rebalance will continue to be a part of your fleet until you terminate them, and will cause your actual capacity to be greater than the fulfilled capacity until you do so.

> It is recommended to provide as many spot capacity pools in the request as possible, and to be flexible with the availability zone.

You can use **instance weighting** to specify the number of units each instance contributes towards the target capacity. The fleet request doesn't know about vCPUs or memory, rather you use the launch specification to say how many unit each instance contributes towards the target capacity. The price of the instance is altered by this, where the _price per instance hour_ becomes the _price per unit hour_.

For example, if you wanted your capacity based on vCPUs and you specified a `m5.8xlarge` with 32 vCPUs, it would contribute 32 units towards the capacity, and its price per unit hour would be $1.536 / 32 = $0.048.

> Instance weighting can cause the fulfilled capacity to be a floating point number, which must be taken into account when setting up things like cloudwatch alarms to trigger when the fulfilled capacity changes. The floating point number will sometimes be rounded up, depending on what other system is analyzing it.

## Spot Fleets

A spot fleet has all the same capability as an EC2 Fleet. The principal difference is where an EC2 Fleet consists of on-demand instances with optional spot instance, a spot fleet consists of spot instances with optional on-demand instances.

Spot Fleets support Application Auto Scaling, which provides the following scaling policies:

-   **target tracking** - increase or decrease the capacity by tracking a particular metric, such as average CPU load across the fleet.
-   **step scaling** - increases the fleet either by a specified number of capacity units, or a percentage of the current capacity.
-   **scheduled scaling** - increase or decrease the capacity based on the date and time.

The fleet must have a request type of `maintain` to use automatic scaling.

## Monitor Fleet Events

There are five event types for EC2 Fleets:

-   **EC2 Fleet State Change**
-   **EC2 Fleet Spot Instance Request Change**
-   **EC2 Fleet Instance Change**
-   **EC2 Fleet Information**
-   **EC2 Fleet Error**

The same five event types are available for spot fleets.
# Monitor
To begin monitoring your EC2 instances, you should create a baseline of performance by observing the instances at different times of day and under different loads. Measure the following metrics during this time:

-   CPU Utilization
-   Network Utilization
-   Disk Performance
-   Disk Reads/Writes
-   Memory utilization, disk swap utilization, disk space utilization, page file utilization, log collection

Monitoring can either be automated using services like CloudWatch Alarms and EventBridge, or it can be done manually using CloudWatch Dashboards.

There are two types of status checks built into EC2: **system status checks** and **instance status checks**. A failed system status check indicates a problem that AWS needs to repair. If you don't want to wait for that you can migrate your instance to a new host (stop and start for EBS-backed, terminate and relaunch for instance-store-backed). A failed instance status check indicates a problem with the instance, such as exhausted memory or incompatible kernel.

A **scheduled event** is a form of maintenance AWS will perform on your instance on your behalf.

## Monitoring with CloudWatch

Metrics for your instance can be collected and stored in CloudWatch for 15 months.

EC2 sends data to CloudWatch in 5 minute intervals by default, or 1 minute intervals if detailed monitoring is enabled for the given instance. When detailed monitoring is enabled, you are charged per metric you send to CloudWatch, not for data storage.
# Networking
# Security
# Storage
# Resource and tags
# Troubleshoot