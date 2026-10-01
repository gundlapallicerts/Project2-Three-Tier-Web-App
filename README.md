# Project 2 — Three-Tier Web Application on AWS

## Overview
This project deploys a Three-Tier Web Application on AWS using CloudFormation.
The architecture separates the web, application, and database layers for security and scalability.

## Architecture
User → Application Load Balancer → EC2 (Auto Scaling) → RDS (MySQL)

## Network Design
- Public subnets host the internet-facing Application Load Balancer and the NAT Gateway.
- The public route table sends `0.0.0.0/0` to the Internet Gateway.
- App instances stay in private subnets without public IPs. Their dedicated route table sends outbound `0.0.0.0/0` traffic through the NAT Gateway so they can install packages and retrieve the site from S3.
- Database subnets remain isolated from internet egress and use only the VPC-local route.
- The NAT Gateway does not allow unsolicited inbound connections to the private EC2 instances.
- NAT Gateways and their public IPv4 addresses incur hourly charges; NAT traffic also incurs data-processing charges. Delete the network stack when the project is no longer needed.

## AWS Region
- Primary: us-east-2 (Ohio)

## Folder Structure
```text
Project2-Three-Tier-Web-App/
├── templates/
│   ├── network.yaml        # VPC, subnets, gateways, and route tables
│   ├── app-tier.yaml       # ALB, EC2 launch template, and Auto Scaling Group
│   └── database-tier.yaml  # RDS instance, subnet group, and security group
├── parameters/
│   └── prod-params.json    # Environment-specific parameters
└── README.md
```

## Deployment Order
1. Deploy network.yaml first (all other stacks depend on it)
2. Deploy app-tier.yaml second
3. Deploy database-tier.yaml last

## Deploy Commands
```bash
# Step 1 - Network
aws cloudformation deploy \
  --template-file templates/network.yaml \
  --stack-name project2-network \
  --region us-east-2

# Step 2 - Application Tier
aws cloudformation deploy \
  --template-file templates/app-tier.yaml \
  --stack-name project2-app \
  --region us-east-2 \
  --capabilities CAPABILITY_NAMED_IAM

# Step 3 - Database Tier
aws cloudformation deploy \
  --template-file templates/database-tier.yaml \
  --stack-name project2-database \
  --region us-east-2
```

## AWS Account
- Account ID: 642528007665
- Region: us-east-2

## Status
- [x] Network stack deployed
- [ ] App tier stack deployed
- [ ] Database stack deployed

