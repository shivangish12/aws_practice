
1. What is Amazon EC2?

Amazon EC2 (Elastic Compute Cloud) provides resizable virtual servers (instances) in the cloud.

Key idea:

You rent compute capacity

You control OS, software, networking

You pay for time + resources

2. AMI (Amazon Machine Image)

An AMI is a template used to launch EC2 instances.

What an AMI contains

OS (Amazon Linux, Ubuntu, Windows, etc.)

Root volume snapshot (EBS-backed)

Default software

Launch permissions

Block device mapping

Important concept

AMI is only used at launch time

If you delete the AMI:

❌ Running instances are NOT affected

✅ Instance keeps running because:

The root EBS volume already exists

AMI is no longer needed after launch

3. Root Volume Types

The root volume depends on the AMI.

Types

EBS-backed (most common)

Instance store–backed (legacy)

Nitro-based instances

Support only EBS root volumes

All modern instance types are Nitro

4. Virtualization Type — HVM

HVM (Hardware Virtual Machine):

Uses hardware-assisted virtualization

Required for modern instance types

Enables:

Better performance

Enhanced networking

NVMe EBS

All current AMIs use HVM

5. EC2 Instance Lifecycle
States

pending → running → stopping → stopped → terminated

Important behaviors

Stop

Instance shuts down

EBS volumes persist

Public IP is released

Start

New public IP assigned

Terminate

Instance deleted

Root EBS deleted (by default)

6. Connecting to EC2 (Linux)
SSH using key pair
ssh -i practice.pem ec2-user@<public-ip>

Permissions issue (very important)
chmod 400 practice.pem


chmod meaning

Changes file permissions

400 → readable only by owner

SSH refuses keys that are publicly readable

7. EC2 Instance Metadata (IMDS)
What is IMDS?

A special internal service that provides instance information:

Instance ID

IAM role credentials

IP addresses

Region

Tags (if enabled)

Special IP
169.254.169.254


This is a link-local address:

Non-routable

Only accessible from inside the instance

Provided by AWS host, not the OS

8. IMDSv2 (Mandatory in production)

IMDSv2 uses session tokens to prevent SSRF attacks.

Step 1: Get token
TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" \
  -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")

Step 2: Use token
curl -H "X-aws-ec2-metadata-token: $TOKEN" \
http://169.254.169.254/latest/meta-data/instance-id


Always remember: IMDSv2 = PUT first, then GET

9. User Data (Bootstrapping EC2)
What is User Data?

Script or config passed at launch

Used for:

Installing packages

Configuring apps

Bootstrapping servers

Key facts

Runs only once by default

Runs as root

Max size: 16 KB

Stored in instance metadata

Not included when creating an AMI

Linux example
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd

View logs
/var/log/cloud-init-output.log

10. IAM Role for EC2
Why IAM Role?

Avoid storing credentials on EC2.

Instead:

Attach an IAM Role

AWS injects temporary credentials

Retrieved via IMDS

You verified this by:
aws s3 ls


This worked because:

EC2 had an IAM role

AWS CLI fetched credentials automatically

11. boto3 with EC2
Common mistake you hit (important)
InvalidInstanceID.NotFound


Cause:

Region mismatch

EC2 instance exists in eu-north-1

boto3 client defaulted to another region

Correct pattern
import boto3

ec2 = boto3.client("ec2", region_name="eu-north-1")

response = ec2.describe_instances(
    InstanceIds=["i-0d2562ae18985b254"]
)


EC2 is region-scoped, always specify region.

12. EC2 Networking Basics
Private IP

Assigned from subnet CIDR

Persistent across stop/start

Used inside VPC

Public IP

Internet-routable

Assigned at launch (optional)

Changes on stop/start

Elastic IP

Static public IP

Belongs to your AWS account

Not released on stop/start

13. DNS Basics (EC2 context)

DNS = Domain Name System

Purpose:

Converts names → IP addresses

Example:

ec2-13-60-18-185.eu-north-1.compute.amazonaws.com
↓
13.60.18.185

EC2 Hostname structure
ip-10-24-34-0.us-west-2.compute.internal


Components:

Hostname: ip-10-24-34-0

Domain: us-west-2.compute.internal

FQDN = full combined name

14. AWS Global Infrastructure (EC2 placement)
Region

Geographical area

Isolated from other regions

Availability Zone (AZ)

Isolated data centers inside a region

Example: eu-north-1a, eu-north-1b

Best practice

Distribute instances across AZs

Avoid single-point failure

15. EC2 Fleets — Conceptual Understanding
EC2 Fleet

Launch many instances at once

Supports:

On-Demand

Spot

Multiple instance types

CLI/API only

No automatic lifecycle management

Spot Fleet

Legacy

Console-supported

No future investment

Auto Scaling Group (ASG)

NOT a fleet type

Higher-level service

Uses EC2 internally

Adds:

Health checks

Auto replacement

Scaling policies

Relationship summary
EC2 → raw compute
EC2 Fleet → bulk launch
Spot Fleet → legacy bulk
ASG → managed lifecycle (recommended)

16. BYOIP (Conceptual)

Bring Your Own IP

Enterprise feature

Use IPs you already own

Preserve reputation & compliance

Not needed for normal EC2 usage

17. Takeaways

AMI used only at launch

IMDS IP = 169.254.169.254

IMDSv2 requires token

EC2 IAM roles = no credentials on disk

Public IP changes on stop/start

EC2 is region-scoped

ASG ≠ EC2 Fleet


1️⃣ What is an Elastic IP?

Static public IPv4 address

Allocated to AWS account, not instance

Remains until explicitly released

Used to mask instance failure by fast remapping

📌 Elastic IP = Fixed public IP for dynamic cloud resources

2️⃣ Why Elastic IP Exists

Without EIP:

Public IP changes on stop/start

DNS breaks

With EIP:

IP never changes

Reassociate to another EC2 in seconds

No DNS propagation delay

3️⃣ Core Properties (Must Memorize)
Property	Value
IP type	Public IPv4 only
Static	Yes
Region-scoped	Yes
IPv6 support	❌ No
Auto released	❌ No
Internet reachable	Yes
4️⃣ Lifecycle of an Elastic IP
Allocate → Associate → Disassociate → Reassociate → Release


⚠️ You are billed until Release

5️⃣ Association Rules (Exam Favorite)

Can associate with:

EC2 instance

Network Interface (ENI)

Always attaches to primary ENI

Reassociation:

Automatically removes old association

Existing public IPv4:

Released permanently

❌ Cannot be converted to EIP

6️⃣ Elastic IP vs Public IPv4
Feature	Public IPv4	Elastic IP
Static	❌	✅
Stop/Start change	Yes	No
Remappable	❌	✅
Charged	Yes	Yes
7️⃣ Pricing ⚠️ (Very Important)

Charged whether used or idle

Charged for all public IPv4 addresses

Disassociated EIP = still billed

📌 AWS discourages overuse due to IPv4 scarcity.

8️⃣ Quota (Limits)

Default: 5 Elastic IPs per Region

Increase via Service Quotas

BYOIP EIPs don’t count toward quota

9️⃣ DNS Behavior

Public DNS updates to match EIP

Resolution:

Outside VPC → Public IP

Inside VPC → Private IP

🔁 10️⃣ Reassociation & Failover Pattern
Instance A fails
↓
EIP reassociated
↓
Instance B active


✔️ Zero DNS change
✔️ Minimal downtime

1️⃣1️⃣ Elastic IP Transfer (Between Accounts)

Ownership transfer (not sharing)

Same Region only

2-step handshake:

Source enables

Target accepts (within 7 days)

❌ BYOIP / IPAM pool EIPs cannot be transferred
❌ Tags are removed after transfer

📧 1️⃣2️⃣ Reverse DNS (PTR) for Email

Required for sending email from EC2

Reduces spam flagging

Requirements:

Forward DNS (A record) must exist

PTR locks Elastic IP:

Cannot release until PTR removed

CLI:

modify-address-attribute
reset-address-attribute

🌐 1️⃣3️⃣ Elastic IP & IPAM

IPAM:

Governs and tracks IPs

Allocates EIPs from pools

IPAM ❌ does NOT transfer EIPs

IPAM pools shared via AWS RAM

1️⃣4️⃣ When to Use Elastic IP

✅ Bastion hosts
✅ Legacy apps needing fixed IP
✅ Email servers
✅ Whitelisted client access

❌ 1️⃣5️⃣ When NOT to Use Elastic IP

❌ Auto Scaling Groups
❌ Load-balanced apps
❌ Microservices
❌ Containers

👉 Use ALB + Route 53 instead

1️⃣6️⃣ Common Exam Traps ⚠️

❌ “Elastic IP is free when unused” → False
❌ “Elastic IP works across regions” → False
❌ “Elastic IP supports IPv6” → False
✅ “Elastic IP is static public IPv4” → True

1️⃣7️⃣ One-Line Exam Summary

Elastic IP is a static, region-scoped public IPv4 address that you control and can remap, but you pay for it until you release it.


Amazon VPC (Virtual Private Cloud)
What is a VPC?

A VPC is a logically isolated virtual network inside AWS.

It closely resembles a traditional on-premise data center network.

You control:

IP address ranges

Subnets

Routing

Internet access

Security

📌 All EC2 networking happens inside a VPC.

VPC CIDR

Defined at VPC creation

Example:

10.0.0.0/16


All subnets and ENIs draw IPs from this range.

Subnets

A subnet is a smaller IP range inside a VPC

Each subnet belongs to exactly one Availability Zone

Used for:

High availability

Security separation

Traffic control

Public vs Private Subnets
Type	Internet Access
Public	Route to Internet Gateway
Private	No direct internet route
Default VPC

AWS automatically creates:

One default VPC per region

One subnet per AZ

Internet Gateway attached

Route:

0.0.0.0/0 → IGW


Public IP auto-assignment enabled

📌 Default VPC = ready-to-use, beginner friendly

Non-default (Custom) VPC

Used in production for:

Private subnets

NAT gateways

Strict security

Controlled routing

Best practices:

Create subnets in multiple AZs

Separate public and private workloads

Internet Access Options
Scenario	Solution
Public EC2	Internet Gateway
Private EC2	NAT Gateway
On-prem connectivity	VPN / Direct Connect
Shared Subnets

One AWS account owns the VPC

Other accounts can launch EC2 into shared subnets

Each account:

Manages its own EC2 & ENIs

Cannot manage others’ instances

IPv6-only Subnets

No IPv4 address assigned

Only IPv6

Requires Nitro-based EC2

Used for large-scale modern networking

2️⃣ Elastic Network Interfaces (ENI)
What is an ENI?

An ENI is a virtual network card in a VPC.

It represents the network identity of an EC2 instance.

Key ENI Attributes

An ENI can have:

Primary private IPv4 address

Secondary private IPv4 addresses

IPv6 addresses

Elastic IP (one per private IPv4)

Security groups

MAC address

Source/Destination check

Description

📌 IPs and security groups belong to the ENI, not the EC2.

Primary vs Secondary ENI
Primary ENI

Created automatically at EC2 launch

Cannot be detached

One per EC2

Secondary ENI

Optional

Can be attached/detached

Can move between EC2s (same AZ)

ENI Scope Rules (VERY IMPORTANT)

ENI is tied to:

Subnet

Availability Zone

ENI cannot move across AZs

ENI can attach only to EC2 in the same AZ

IP Addressing Rules
Private IPv4

Primary private IP → fixed

Secondary IPs → movable

Public IPv4

Assigned based on subnet setting

Released when EC2 stops

Reassigned on restart

Elastic IP

Static public IPv4

Region-scoped

Attached to private IP on ENI

Source/Destination Check

Enabled by default

Must be disabled for:

NAT instance

Firewall

Router

ENI Termination Behavior

ENI can be:

Deleted with EC2

Preserved after EC2 termination

Managed & Requester-Managed ENIs

Created by AWS services (ELB, Lambda, NAT Gateway)

Cannot be modified or detached manually

3️⃣ Attaching & Detaching ENIs
When can ENIs be attached?
AWS Term	Meaning
Cold attach	During launch
Warm attach	EC2 stopped
Hot attach	EC2 running
Detachment Rules

Secondary ENIs → detachable

Primary ENI → never detachable

Multi-homed EC2

EC2 with multiple ENIs

Used for:

Traffic separation

Multi-VPC access

Firewalls / NAT

⚠️ More ENIs ≠ more bandwidth

Public IP Behavior with Multiple ENIs

If EC2 has >1 ENI:

AWS will NOT auto-assign public IP

Use Elastic IP instead

4️⃣ Prefix Delegation (Advanced ENI Feature)
What is Prefix Delegation?

Assigning a CIDR block (IP range) to an ENI instead of individual IPs.

Used mainly for:

Containers

Kubernetes / EKS

High-density workloads

Prefix Sizes
Type	Prefix
IPv4	/28 (16 IPs)
IPv6	/80
Why Prefix Delegation?

Without prefix:

Each container requests IP from EC2 API

With prefix:

ENI already owns a pool of IPs

Containers assign IPs locally

Faster scaling

Where Prefix Lives
Subnet
  ↓
ENI  ← Prefix (/28 or /80)
  ↓
EC2
  ↓
Containers

Automatic vs Manual Prefix Assignment

Automatic: AWS chooses free prefix

Manual: You specify CIDR (no overlap allowed)

ENI Limits with Prefixes

Each prefix counts as 1 IP toward ENI limit

Example:

ENI allows 10 IPs

1 primary IP used

9 prefixes allowed

9 × 16 = 144 usable IPs

Managing Prefixes

You can:

Assign prefixes during ENI creation

Add prefixes later

Remove prefixes

⚠️ Removing a prefix:

Removes all IPs in that range

Breaks apps using them

Elastic IP with Prefixes

Elastic IP can be:

Attached to ENI

Attached to an IP inside the prefix






1️⃣ BLOCK STORAGE (Disk-like storage)
🔹 Amazon EBS (Elastic Block Store)

Amazon Elastic Block Store

What it is

Persistent block storage for EC2

Works like an external hard disk

Network-attached

Key points

Data persists across:

Reboot ✅

Stop ✅

Data can be:

Detached

Reattached to another instance

Backed up using snapshots

Used for:

OS

Databases

Application data

EBS Volume Limits

Depends on instance type & size

Most Nitro instances:

28 total attachments
= EBS volumes + ENIs + NVMe disks


Some newer instances have dedicated EBS limits (no subtraction)

Exceed limit → AttachmentLimitExceeded

🔹 Root Volumes

Root volume = disk that contains the OS

Usually an EBS volume

Deleted on termination ❌ (default)

Options

Keep root volume after termination ✅

Replace root volume

Modify via block device mapping

2️⃣ INSTANCE STORE (Temporary Storage)
🔹 EC2 Instance Store

Amazon EC2 Instance Store

What it is

Temporary block storage

Physically attached to EC2 host

Very fast

Free (included in EC2 cost)

What you must remember

Data does NOT persist

Cannot be:

Detached

Reattached

Snapshotted

Must be added at launch only

Use cases

Cache

Buffers

Scratch data

Temporary replicated data

🔹 Instance Store Data Persistence (IMPORTANT)
Golden Rule 🧠
Reboot → Data stays
Stop / Hibernate / Terminate → Data LOST

Why data is lost?

Data is cryptographically erased

Encryption keys destroyed

If data matters → copy to:

Amazon EBS

Amazon S3

Amazon EFS

3️⃣ SSD INSTANCE STORE VOLUMES
Types

NVMe SSD (modern)

Non-NVMe SSD (older: C3, I2, M3, R3, X1)

NVMe SSD facts

Extremely fast

Always hardware-encrypted

Encryption:

Automatic

Keys destroyed on stop/terminate

You:

❌ cannot disable encryption

❌ cannot bring your own key

SSD Performance Behavior

⚠️ As SSD fills up → performance decreases

Reason:

SSD must do internal cleanup (write amplification)

Best Practices

Leave ~10% disk unpartitioned

Use TRIM to tell SSD which data is no longer needed

4️⃣ INSTANCE STORE VOLUME MANAGEMENT
Add Instance Store Volumes

Can only be added:

At instance launch

Via AMI

Cannot be attached later

After launch

You must:

Initialize

Format

Mount

Make available for use

Swap Volumes (Old Instances)

Used in M1, C1 families

Instance store used as swap memory

Rare today (mostly historical)

5️⃣ BLOCK DEVICE MAPPINGS
What it means

How disks are attached and named inside the OS

You can define mappings:

While creating an AMI

While launching an instance

Includes:

Root volume

EBS volumes

Instance store volumes

Device Names

Linux: /dev/xvda, /dev/nvme0n1

Windows: Disk 0, Disk 1, etc.

NVMe Mapping

NVMe disks don’t show classic names

Must map:

NVMe device → EBS Volume ID

6️⃣ ADVANCED STORAGE CONCEPTS
Torn Write Prevention

Prevents partial writes during failure

Important for databases

Supported Block Sizes

Some workloads require aligned I/O sizes

Check Instance Support

Not all instances support:

Instance store

NVMe

High IOPS

Always check instance specs.

7️⃣ STORAGE BEYOND EC2
🔹 Amazon S3 (Object Storage)

Amazon S3

Object-based storage

Extremely durable

Cheap

Not mounted like a disk

Used for:

Backups

Images

Logs

Data lakes

🔹 Amazon EFS (File Storage)

Amazon EFS

Shared file system

Mountable on multiple EC2s

POSIX-compliant

Used for:

Shared app data

CMS

Multi-instance apps

🔹 Amazon FSx (High-performance File Systems)

Amazon FSx

Enterprise-grade file systems

Windows, Lustre, NetApp

Used for:

Windows workloads

HPC

Enterprise apps

🔹 Amazon File Cache

Amazon File Cache

Cache layer in front of S3

Low latency

High throughput

🧠 ONE-PAGE CHEAT SHEET
EBS = persistent disk
Instance Store = fast but temporary
Root volume = OS disk
Reboot ≠ Stop
NVMe = encrypted by default
Block mapping = disk wiring
S3 = object storage
EFS = shared file system
FSx = enterprise file system
File Cache = speed layer



/etc/fstab for auto mounting

AWS has two layers of network security:

Subnet-level security  → NACL
Instance-level security → Security Group
Think:
NACL = gate at society entrance
Security Group = lock on your house door
SG = Stateful & Simple
NACL = Network-level & Needs both directions

