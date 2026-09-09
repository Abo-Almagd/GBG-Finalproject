# GBG Multi-Region AWS Migration & Disaster Recovery

Migration of Global Brands Group's on-premises environment to a hub-and-spoke AWS landing zone, with a primary production region and a secondary disaster-recovery (DR) region, using AWS MGN / DMS for cutover and AWS Elastic Disaster Recovery for ongoing replication.

> Replace the placeholders below (name, regions, repo links) with your project's specifics before publishing.

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
  - [Theoretical (Target) Architecture](#theoretical-target-architecture)
  - [Actual (Implemented) Architecture](#actual-implemented-architecture)
- [Migration Approach](#migration-approach)
- [Cost Estimate](#cost-estimate)
- [Repository Structure](#repository-structure)
- [Getting Started](#getting-started)
- [Status](#status)
- [License](#license)

## Overview

This project stands up a hub-and-spoke AWS network across a primary and a DR region, migrates on-premises web and database workloads into it, and wires up monitoring, security, and backup so the environment is production-ready and resilient to a regional failure.

Key components:

- **Networking:** Hub VPC (NAT, ALB, AWS Network Firewall, Transit Gateway) peered to a Spoke VPC (App + RDS subnets) via Transit Gateway, per region.
- **Edge:** Route 53, CloudFront, AWS WAF, and AWS Shield in front of the application.
- **Compute:** Auto Scaling Group of web servers behind an internal NLB and public ALB.
- **Data:** Amazon RDS for MySQL (Multi-AZ in the primary region, cross-region read replica in DR).
- **Migration tooling:** AWS Application Migration Service (MGN) for web servers, AWS DMS for database migration/CDC.
- **DR:** AWS Elastic Disaster Recovery (DRS), RDS cross-region read replica promotion, and Route 53 failover routing.
- **Security & compliance:** GuardDuty, Security Hub, AWS Config, Macie, CloudTrail, Network Firewall.
- **Operations:** CloudWatch dashboards/alarms, AWS Backup, SNS notifications.

## Architecture

### Theoretical (Target) Architecture

The initial design proposed two full, symmetric hub-and-spoke stacks — one per region — each with its own Hub/Spoke VPC pair, Transit Gateway, Network Firewall, and Bastion, sitting behind a shared edge layer (Shield, WAF, Route 53) and a common set of management/security services (Config, GuardDuty, Macie, Backup, CloudTrail).

![Theoretical Architecture](diagrams/theoretical-architecture.jpeg)

### Actual (Implemented) Architecture

The implemented design keeps the same hub-and-spoke pattern in the **primary region**, but the **DR region** was scoped down to a minimal footprint (a single public subnet with a compute instance and a private RDS instance) to control cost while still meeting the recovery objectives. The primary region's hub still connects to the DR region over the Transit Gateway/VPN, and the same edge and security/management stack fronts both.

![Actual Architecture](diagrams/actual-architecture.jpeg)

The editable source diagram is included at [`diagrams/actual-architecture.drawio`](diagrams/actual-architecture.drawio) — open it with [draw.io / diagrams.net](https://app.diagrams.net/).

## Migration Approach

The migration was executed in five phases (full detail in [`docs/Project-Plan.pdf`](docs/Project-Plan.pdf)):

| # | Phase | Summary |
|---|-------|---------|
| 1 | On-Premises Discovery Before Migration | Run AWS Application Discovery Service, document server/DB inventory, network and firewall rules. |
| 2 | AWS Infrastructure Setup (Hub & Spoke) | Build Hub/Spoke VPCs, ALB + WAF, Transit Gateway routing, AWS Network Firewall, Bastion, and the site-to-site VPN back to on-premises. |
| 3 | Migration & DR Implementation | Install MGN/DMS agents, replicate and cut over web servers and databases, stand up the ASG/NLB in the spoke, and validate production. |
| 4 | Security, Compliance & Operations | Enable DRS and RDS cross-region replication, build out the DR region, harden security (GuardDuty, Security Hub, Config, Macie), and configure AWS Backup. |
| 5 | Monitoring, Alerting & Observability | Stand up CloudWatch dashboards, alarms, log groups, and SNS notifications end-to-end. |

## Cost Estimate

AWS Pricing Calculator estimate for the environment (see [`docs/GBG_SIZING.pdf`](docs/GBG_SIZING.pdf) / [`docs/GBG_SIZING.csv`](docs/GBG_SIZING.csv) for the full line-item breakdown):

| Group | Monthly Cost |
|---|---|
| Frankfurt Pricing Group | $462.26 |
| N. Virginia Pricing Group | $3,336.42 |
| Global Services (Route 53, CloudFront, WAF) | $594.96 |
| **Total** | **$4,393.64 / mo (~$52,723.68 / yr)** |

## Repository Structure

```
.
├── README.md
├── diagrams/
│   ├── theoretical-architecture.jpeg
│   ├── actual-architecture.jpeg
│   └── actual-architecture.drawio
└── docs/
    ├── Project-Plan.pdf
    ├── GBG_SIZING.pdf
    └── GBG_SIZING.csv
```

> Adjust this tree to match how you actually organize the repo — e.g. if you add Terraform/CloudFormation code, note the `infra/` or `terraform/` folder here too.

## Getting Started

1. Review the [Theoretical Architecture](#theoretical-target-architecture) and [Actual Architecture](#actual-implemented-architecture) diagrams to understand the network design.
2. Follow the phase breakdown in [Migration Approach](#migration-approach) / `docs/Project-Plan.pdf` for the build order.
3. Use `docs/GBG_SIZING.csv` as the basis for a cost review before provisioning.
4. *(If this repo contains IaC)* add setup/deploy instructions here — prerequisites, `terraform init/plan/apply` or equivalent, and required variables/secrets.

## Status

Infrastructure setup, migration, and DR phases are documented and tracked in the project plan; update this section with current progress (e.g. "Phases 1–3 complete, Phase 4 in progress").

## License

Add your license here (e.g. MIT, Apache-2.0), or mark as proprietary/internal if this is not meant to be open source.
