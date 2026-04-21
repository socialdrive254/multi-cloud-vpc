# AWS Multi-Tier VPC Architecture

A production-style VPC on AWS with public, private, and isolated subnets
across two availability zones, demonstrating secure network segmentation,
controlled internet access, and defense-in-depth using security groups and NACLs.

## Architecture

![Architecture Diagram](diagrams/architecture.png)

## What this project demonstrates

- VPC design and CIDR planning
- Public/private/isolated subnet segmentation
- Internet Gateway and NAT Gateway routing
- Security groups vs NACLs (stateful vs stateless filtering)
- Bastion host / SSM Session Manager access patterns
- Multi-AZ design for high availability

## Tech stack

- AWS (VPC, EC2, RDS, NAT Gateway, Internet Gateway)
- (Later) Terraform for infrastructure-as-code

## Documentation

1. [Planning & CIDR design](docs/01-planning.md)
2. [VPC and subnet setup](docs/02-vpc-setup.md)
3. [Routing configuration](docs/03-subnets-routing.md)
4. [Security groups and NACLs](docs/04-security.md)
5. [Testing the architecture](docs/05-testing.md)
6. [Lessons learned](docs/06-lessons-learned.md)

## Status

🚧 In progress
