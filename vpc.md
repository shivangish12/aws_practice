## 🛡️ Overview

Amazon Virtual Private Cloud (VPC) lets you provision a logically isolated section of the AWS Cloud where you can launch AWS resources in a virtual network that you define.

Key properties:
- Region-level isolation
- Subnets
- Custom route tables
- ACLs, security groups
- Private/public resources
- Integrations with AWS networking/security services

---

## 🧱 Basic VPC Concepts

| Term | Definition |
|------|------------|
| **VPC** | Logical isolated network in AWS. |
| **CIDR Block** | IP range for the VPC (e.g., `10.0.0.0/16`). |
| **AZ** | Availability Zone (fault isolation). |
| **Subnet** | Subset of VPC CIDR in an AZ. |
| **ENI** | Elastic Network Interface (secondary IPs). |

---

## 📍 Subnets & IP Addressing

- **Public subnet**: Has route to IGW (internet gateway).  
- **Private subnet**: No direct internet access.  
- **Auto-assigned public IPs**: Controlled per subnet.  
- IPv4 & IPv6 supported.

### CIDR Planning
- Typical VPC: `10.0.0.0/16`
- Subnet: `10.0.1.0/24`
- Must avoid overlapping CIDRs when peering.

---

## 🛣️ Route Tables & Routing

- Controls traffic routing within VPC.
- Each subnet associates with one route table.
- Default route table created per VPC.
## 🌐 Internet Connectivity

### Internet Gateway (IGW)
- Allows inbound/outbound internet connectivity.
- Attach to VPC, routes allow internet.

### NAT Gateway / NAT Instance
- Allows private subnet to access internet (outbound).
- NAT Gateway: managed, scalable, HA within AZ.

### Egress-only IGW (IPv6)
- IPv6 NAT equivalent for outbound-only IPv6 traffic.

---

## 🔐 Security Controls

### Security Groups (SG)
- Stateful instance-level firewall.
- Allow rules only.
- Auto incoming session tracking.

### Network ACLs (NACL)
- Stateless subnet-level firewall.
- Both allow/deny.
- Order-based evaluation.

### Comparison: Security Group vs NACL

| Aspect | SG | NACL |
|--------|----|------|
| Stateful | Yes | No |
| Rules | Allow only | Allow + Deny |
| Level | Instance | Subnet |
| Return traffic | Implicitly allowed | Must explicitly allow |
  
---

## 🔗 VPC Connectivity Options

### VPC Peering
- Connects two VPCs privately.
- No transitive routing.
- Same or different accounts/regions (cross-region peering).
- Must avoid overlapping CIDRs.

### AWS Transit Gateway (TGW)
- Hub-and-spoke network connectivity.
- Scales to thousands of VPCs.
- Supports VPN, DX, TGW attachments.
- Centralized routing control.

---

## 🔒 AWS VPN

### Site-to-Site VPN
- IPsec tunnels between on-prem and AWS VPC (VGW or TGW).
- Two tunnels for HA.

### AWS Client VPN
- TLS-based remote user VPN using OpenVPN.

### VPN CloudHub
- Connect multiple sites using VGW peering model.

---

## 🛡️ VPC Encryption Controls

Encryption Controls provide central enforcement of **encryption in transit**:
- Monitor mode (audit encryption status).
- Enforce mode (block unencrypted flows).
- Works with Nitro hardware and app-layer encryption (TLS).
- Inspect VPC flow logs for encryption status (`encryption-status` field).

**Modes:**
1. **Monitor** – Visibility only.
2. **Enforce** – Prevent unencrypted traffic.

---

## 🚫 VPC Block Public Access (VPC BPA)

VPC BPA blocks internet traffic for security governance.

### Modes

| Mode | Inbound | Outbound |
|------|---------|----------|
| **Bidirectional** | Block | Block |
| **Ingress-only** | Block | Allow |

- Applies at account/region level.
- Use exclusions to allow specific VPCs/subnets.

---

## 🔥 AWS Network Firewall

A managed network firewall service for filtering network traffic at the VPC perimeter.

### Components

| Term | Description |
|------|-------------|
| **Firewall** | Deployed in subnets to filter traffic. |
| **Firewall Policy** | Defines rules to inspect/allow/deny traffic. |
| **Rule Groups** | Stateless or stateful rule sets. |

- Stateful rule groups track session state.
- Stateless rule groups inspect packets individually.
- Integrates with AWS Firewall Manager for centralized governance.

---

## 🛡️ Route 53 Resolver DNS Firewall

DNS-level filtering to allow/block DNS queries:

- **Rule Groups**: sets of DNS filtering rules.
- **Domain Lists**: reusable lists of domains to match.
- **Actions**: Allow, Block, Alert, Custom response.

Works with VPC DNS resolver to block/responses at DNS time.

---

## 📊 Monitoring & Logging

### VPC Flow Logs
- Capture IP traffic metadata.
- Send to CloudWatch or S3.
- Use for security, analysis, billing, auditing.

### AWS CloudWatch
- Metric alerts, dashboards for network health.

### AWS GuardDuty
- Threat detection across VPC traffic, DNS logs, S3 logs, API calls.

---

## 🧠 Best Practices

### Security

- Use least privilege SGs (restrict source IP/ports).
- Avoid 0.0.0.0/0 for sensitive ports (22, 3389).
- Use NACLs for extra subnet-level filtering.
- Deploy perimeter firewall (Network Firewall) for deep inspection.
- Use DNS Firewall to block malicious domains.

### Design

- Plan CIDR blocks to avoid overlap.
- Use private subnets for backend resources.
- Use appropriate NAT/IGW placement.
- Separate workloads by AZ for high availability.

### Connectivity

- Use Transit Gateway for scalable multi-VPC connectivity.
- Prefer PrivateLink for private service access without public IPs.
- Use Direct Connect + VPN for hybrid connectivity.

### Monitoring & Compliance

- Enable VPC Flow Logs in all VPCs.
- Integrate GuardDuty + Security Hub.
- Implement encryption in transit (Encryption Controls).


## 📡 DHCP in Amazon VPC (Dynamic Host Configuration Protocol)

---

### 🔹 What is DHCP?

> **DHCP automatically assigns network configuration to instances** when they launch.

This includes:
- IP address
- Subnet mask
- Default gateway
- DNS server
- Domain name

---

### 🧠 Simple meaning

Without DHCP:
> You would manually configure IP, DNS, gateway for every EC2 😵

With DHCP:
> AWS auto-assigns everything on boot 🚀

---

## 🧱 DHCP in AWS VPC

AWS provides **managed DHCP** inside every VPC.

When an EC2 instance launches, it automatically gets:
- Private IP
- DNS resolver
- Default gateway
- Routing config

No manual setup required.

---

## 📘 DHCP Options Set (Very Important Concept)

> A **DHCP Options Set** is a configuration object that controls:
- DNS servers
- Domain name
- NTP servers
- NetBIOS servers
- NetBIOS node type

---

### Default DHCP Options in VPC

By default, AWS sets:

| Parameter | Default Value |
|------|------|
| DNS Server | AmazonProvidedDNS |
| Domain Name | region.compute.internal |
| NTP Server | AWS Time Sync Service |
| NetBIOS | Not configured |
