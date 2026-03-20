Server is composed of:
- CPU
- RAM
- Storage
- Database
- Network


Advantages of cloud computing:
- CAPEX for OPEX
- Benefit from massive economies of scale
- Stop guessing capacity
- Increase speed and agility
- Money not spent on running and maintaining data centers
- Go global in minutes

Types of CC:
- IaaS
- PaaS
- SaaS

Pricing:
- Compute
- Storage
- Data transfer out of the cloud

Each region has 3-6 AZs. Each availability zone (AZ) is one or more discrete data centers with redundant power, networking, and connectivity.


# IAM:
- Users, groups(cannot contain groups),Roles, Policies
- Groups can be assigned policies.
- In AWS you apply the least privilege principle: don’t give more permissions than a user needs.
- Policies structure:
  - Version
  - Id
  - Statement
      - Sid
      - Effect
      - Principal
      - Action
      - Resource
      - Condition


# EC2:
- EC2 Instance: AMI (OS) + Instance Size (CPU + RAM) + Storage + security groups + EC2 User Data
- Purchasing Options: On-Demand, Spot, Reserved (Standard + Convertible), Dedicated Host, Dedicated Instance
## EBS Volume:
An EBS (Elastic Block Store) Volume is a network drive you can attach to your instances while they run
• It allows your instances to persist data, even after their termination
• They can only be mounted to one instance at a time (at the CCP level)
• They are bound to a specific availability zone
- EBS Snapshots
## AMI- Amazon Machine Image:
- are built for a specific region
## EC2 Instance Store
- EBS volumes are network drives with good but “limited” performance
- If you need a high-performance hardware disk, use EC2 Instance Store
- ephemeral
## EFS (Elastic File System)
multi-AZ
EFS-IA
- EFS will automatically move your files to EFS-IA based on the last time they were accessed
## Amazon FSx
-  Launch 3rd party high-performance file systems on AWS
-  Fully managed service
## ELB and ASG
- Scalability can be horizontal(elasticity)(increasing the number of instances/systems for your application) or vertical( increasing the size of the instance)
-  High Availability usually goes hand in hand with horizontal scaling
-  High availability means running your application/system in at least 2 Availability Zones
-  Load balancers are servers that forward internet traffic to multiple servers (EC2 Instances) downstream.

   4 kinds of load balancers offered by AWS:
• Application Load Balancer (HTTP / HTTPS only) – Layer 7
• Network Load Balancer (ultra-high performance, allows for TCP) – Layer 4
• Gateway Load Balancer – Layer 3
• Classic Load Balancer (retired in 2023) – Layer 4 & 7

#S3






