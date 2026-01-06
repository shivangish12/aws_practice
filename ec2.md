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
