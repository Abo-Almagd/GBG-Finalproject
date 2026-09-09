# AWS Multi-Region Secure & Resilient Cloud Architecture

> **Portfolio Project — Enterprise AWS Infrastructure, Security, Disaster Recovery & Migration**

## 📌 Overview

This project presents a **multi-region AWS architecture** designed for a secure, highly available, and resilient enterprise workload.

The architecture uses **US East (N. Virginia)** as the primary environment and **Europe (Frankfurt)** as a Disaster Recovery (DR) environment. It also includes a dedicated spoke VPC, centralized connectivity, network security controls, monitoring, backup, and migration capabilities.

The design focuses on:

- 🔐 Defense-in-depth security
- 🌍 Multi-region disaster recovery
- ⚡ High availability and scalable application delivery
- 🔄 Hybrid connectivity and workload migration
- 📊 Centralized monitoring and security visibility
- 💾 Database backup and replication
- 💰 Infrastructure sizing and cost estimation

---

## 🏗️ Architecture Diagram

![AWS Architecture Diagram](./Actual%20Arch.jpeg)

---

## 🌎 High-Level Architecture

### Primary Region — US East (N. Virginia)

The primary workload is hosted in **US East (N. Virginia)** and is built around a VPC using multiple Availability Zones.

Key components include:

- Application Load Balancer (ALB)
- Network Load Balancer (NLB)
- EC2 Auto Scaling environment
- Amazon RDS for MySQL — Multi-AZ
- NAT Gateway
- AWS Network Firewall
- Bastion Host
- Transit Gateway connectivity
- Amazon S3
- AWS MGN replication infrastructure
- AWS DMS
- CloudWatch
- CloudTrail
- GuardDuty
- Security Hub
- AWS Config
- Amazon Macie
- AWS Backup
- Lambda
- EventBridge
- SNS
- AWS Certificate Manager

### Disaster Recovery Region — Frankfurt

The Frankfurt environment provides a **regional DR capability** and contains:

- EC2-based DR instance
- Amazon RDS for MySQL
- Application Load Balancer
- NAT Gateway
- Bastion Host
- CloudWatch
- AWS Certificate Manager

The architecture also includes database migration/replication capabilities between the primary and DR environments.

---

## 🔐 Security Architecture

Security is implemented using multiple layers rather than relying on a single control.

### Edge Security

- **Amazon Route 53** for DNS
- **Amazon CloudFront** for global content delivery
- **AWS WAF** for web application protection
- AWS security services integrated into the environment

### Network Security

- VPC isolation
- Public and private subnet segmentation
- Security Groups
- Network Firewall
- NAT Gateway for controlled outbound access
- Transit Gateway for VPC connectivity
- Site-to-Site VPN for hybrid connectivity

### Security Monitoring & Compliance

- **Amazon GuardDuty** — threat detection
- **AWS Security Hub** — centralized security findings
- **AWS CloudTrail** — API/activity auditing
- **AWS Config** — configuration tracking and compliance checks
- **Amazon Macie** — sensitive data discovery and protection
- **Amazon CloudWatch** — metrics, logs, dashboards and alarms

---

## 🔄 Disaster Recovery & Migration

The design supports both **disaster recovery** and **on-premises migration** scenarios.

### AWS Application Migration Service (MGN)

AWS MGN is used as part of the migration path from an on-premises environment into AWS.

The sizing includes a dedicated replication server in the primary region.

### AWS Database Migration Service (DMS)

AWS DMS is included to support database migration between the primary and DR environments.

The sizing includes a DMS replication instance in **US East (N. Virginia)** for migration toward **Frankfurt**.

### AWS Backup

AWS Backup is included for centralized backup management and long-term retention.

The sizing model includes backup workloads with up to **180 days of warm retention**.

---

## 🌐 Networking Design

The networking layer follows a segmented VPC architecture.

### Primary VPC

`10.0.0.0/16`

The primary environment is divided into dedicated subnets for application, database, NAT, firewall, and bastion components across Availability Zones.

### Spoke VPC

`10.1.0.0/16`

A separate spoke VPC is connected through the centralized Transit Gateway architecture.

### DR VPC

`10.0.0.0/16`

The Frankfurt DR environment contains isolated public and private resources supporting the recovery workload.

> **Note:** CIDR blocks shown above are taken from the architecture diagram. In a production implementation, overlapping CIDRs between independently connected VPCs would need to be avoided or addressed through an appropriate networking design.

---

## ⚙️ Application Traffic Flow

A simplified external request path is:

```text
Users
  │
  ▼
Route 53
  │
  ▼
CloudFront
  │
  ▼
AWS WAF
  │
  ▼
Application Load Balancer
  │
  ▼
Application / EC2 Auto Scaling
  │
  ▼
Amazon RDS for MySQL
```

Supporting network traffic is controlled through:

```text
VPC
 ├── Public Subnets
 │    ├── Load Balancers
 │    └── Bastion Host
 │
 ├── Private Application Subnets
 │    └── EC2 / Application Tier
 │
 ├── Private Database Subnets
 │    └── Amazon RDS
 │
 ├── NAT Gateway
 │
 └── AWS Network Firewall
```

---

## 📊 Infrastructure Sizing

The architecture was sized using the **AWS Pricing Calculator**.

The exported estimate is dated **July 21, 2026**.

| Metric | Estimate |
|---|---:|
| Upfront Cost | **$0.00** |
| Monthly AWS Cost | **$4,393.64** |
| Estimated 12-Month Cost | **$52,723.68** |

### Regional Cost Breakdown

| Pricing Group | Monthly Estimate |
|---|---:|
| US East (N. Virginia) | **$3,336.42** |
| Europe (Frankfurt) | **$462.26** |
| Global Services | **$594.96** |
| **Total** | **$4,393.64** |

> AWS Pricing Calculator values are estimates. Actual AWS charges depend on real usage, configuration, data transfer, pricing changes, and applicable taxes.

---

## 🧰 AWS Services Used

### Compute
- Amazon EC2
- EC2 Auto Scaling workload
- AWS Lambda

### Networking
- Amazon VPC
- Subnets
- Route Tables
- Internet Gateway
- NAT Gateway
- Transit Gateway
- Site-to-Site VPN
- Application Load Balancer
- Network Load Balancer
- AWS Network Firewall
- Elastic Network Interfaces
- Public IPv4

### Database & Storage
- Amazon RDS for MySQL
- Amazon S3
- AWS Backup

### Edge & DNS
- Amazon Route 53
- Amazon CloudFront
- AWS WAF
- AWS Certificate Manager

### Security
- Amazon GuardDuty
- AWS Security Hub
- AWS Config
- Amazon Macie
- AWS CloudTrail

### Monitoring & Operations
- Amazon CloudWatch
- Amazon EventBridge
- Amazon SNS

### Migration
- AWS Application Migration Service (MGN)
- AWS Database Migration Service (DMS)

---

## 🎯 Key Design Goals

### 1. High Availability

The primary workload uses multiple Availability Zones and a Multi-AZ database deployment to reduce the impact of infrastructure failures.

### 2. Disaster Recovery

A dedicated Frankfurt environment provides a second AWS region for regional recovery scenarios.

### 3. Defense in Depth

Security is distributed across the edge, network, workload, identity, logging, detection, and compliance layers.

### 4. Controlled Network Access

Private application and database resources are isolated from direct internet exposure, while NAT Gateway and Network Firewall provide controlled traffic paths.

### 5. Centralized Visibility

CloudWatch, CloudTrail, GuardDuty, Security Hub, and AWS Config provide operational and security visibility.

### 6. Migration Readiness

AWS MGN and AWS DMS provide mechanisms for migrating workloads and databases from on-premises environments and between AWS regions.

---

## 📁 Project Structure

```text
.
├── Actual Arch.jpeg
├── GBG SIZING.pdf
├── GBG SIZING.csv
└── README.md
```

---

## 💡 Skills Demonstrated

This project demonstrates practical experience with:

- AWS VPC architecture
- Multi-AZ and multi-region design
- AWS networking
- Hybrid connectivity
- Transit Gateway
- Load balancing
- Network security
- Cloud security services
- Infrastructure monitoring
- Disaster recovery architecture
- Database migration
- Server migration
- Backup strategy
- AWS cost estimation
- Enterprise cloud architecture documentation

---

## ⚠️ Architecture Notes

This repository represents an **architecture/design project and sizing exercise** based on the provided architecture diagram and AWS Pricing Calculator estimate.

The AWS Pricing Calculator estimate should not be interpreted as an actual AWS bill, and the architecture diagram should be validated against the final production requirements before implementation.

---

## 👨‍💻 Author

**Mohamed Aboelmagd**

Cloud / AWS Infrastructure & Security Enthusiast

---

## ⭐ Project Highlights

**Multi-Region | High Availability | Disaster Recovery | Network Security | Cloud Security | Hybrid Connectivity | Migration | Monitoring | Cost Optimization**
