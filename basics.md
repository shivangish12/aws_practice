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

# S3
- buckets (globally unique name (across all regions all accounts)), objects
- buckets are defined at region level
- Objects have a key
- keys=prefix+object name
- max object size is 5 TB
- S3 Storage Classes
    - Amazon S3 Standard - General Purpose
    - Amazon S3 Standard-Infrequent Access (IA)
    - Amazon S3 One Zone-Infrequent Access
    - Amazon S3 Glacier Instant Retrieval - minimum storage duration of 90 days, Millisecond retrieval, great for data accessed once a quarter
    - Amazon S3 Glacier Flexible Retrieval - Expedited (1 to 5 minutes), Standard (3 to 5 hours), Bulk (5 to 12 hours) – free. Minimum storage duration of 90 days
    - Amazon S3 Glacier Deep Archive -  Standard (12 hours), Bulk (48 hours). Minimum storage duration of 180 days
    - Amazon S3 Intelligent Tiering - Moves objects automatically between Access Tiers based on usage

   S3 Express One Zone -  Objects stored in a Directory Bucket (bucket in a single AZ). Handle 100,000s requests per second with single-digit millisecond latency.
  AWS storage gateway- Hybrid storage service to allow on- premises to seamlessly use the AWS Cloud

# Databases
  ## RDS
  We can’t SSH into instances.
  ## Aurora
  - AWS cloud optimised.
## RDS Deployments
- Read Replicas:
• Scale the read workload of your DB
• Can create up to 15 Read Replicas
• Data is only written to the main DB
## Elasticache
ElastiCache is to get managed Redis or Memcached

## DynamoDB
DAX: Fully managed in-memory cache for DynamoDB




