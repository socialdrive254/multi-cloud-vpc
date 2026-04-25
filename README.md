# Multi-Cloud VPC Architecture
 
A production-style VPC built on both AWS and GCP, demonstrating secure
network segmentation, controlled internet access, and high availability
across multiple availability zones.
 
## Why multi-cloud?
 
Building the same architecture on two platforms proves understanding of
networking fundamentals rather than vendor-specific knowledge. The design
decisions, CIDR planning, and security model are identical — only the
implementation differs.
 
## Architecture
 
### GCP (active)
![GCP Architecture](gcp/diagrams/architecture.png)
 
### AWS (planned)
![AWS Architecture](aws/diagrams/architecture.png)
 
## GCP implementation
 
| Subnet   | CIDR          | Region        | Purpose                      |
|----------|---------------|---------------|------------------------------|
| public   | 10.0.1.0/24   | europe-west1  | Web servers                  |
| private  | 10.0.11.0/24  | europe-west1  | Application servers          |
| isolated | 10.0.21.0/24  | europe-west1  | Database (no internet)       |
 
### Documentation
1. [Planning & CIDR design](gcp/docs/01-planning.md)
2. [VPC and subnet setup](gcp/docs/02-vpc-setup.md)
3. [Firewall rules and routing](gcp/docs/03-firewall-routing.md)
4. [Testing the architecture](gcp/docs/04-testing.md)
5. [Lessons learned](gcp/docs/05-lessons-learned.md)
## AWS implementation (on hold)
 
1. [Planning & CIDR design](aws/docs/01-planning.md)
## Status
 
🟢 GCP — in progress
🟡 AWS — on hold (resuming soon)