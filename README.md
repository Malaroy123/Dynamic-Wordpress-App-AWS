# Dynamic-Wordpress-App-AWS
This repository contains the resources and scripts used to deploy a WordPress website on Amazon Web Services (AWS).
# Architecture Overview

The deployment includes:

- **VPC with public and private subnets** across two Availability Zones for redundancy.
- **Security Groups** to regulate traffic and enhance security.
- **NAT Gateway** for controlled internet access from private subnets.
- **Application Load Balancer (ALB)** for distributing traffic across multiple EC2 instances.
- **Auto Scaling Group (ASG)** for dynamic instance scaling based on demand.
- **Amazon RDS** for managed relational database services.
- **Amazon EFS** for shared storage across multiple instances.
- **AWS Certificate Manager** for managing SSL/TLS certificates.
- **Amazon Route 53** for domain name resolution and DNS management.

# Setup Instructions

## Prerequisites

- An AWS account
- IAM permissions for VPC, EC2, RDS, EFS, ALB, and Route 53
- Basic knowledge of AWS networking and security

## Deployment Steps

### 1. Create a VPC

- Define CIDR block and subnets (public/private across multiple AZs).
- Configure Internet Gateway and route tables.

### 2. Set Up Security Groups

- Create groups for ALB, EC2, RDS, EFS, and EICE with appropriate rules.

### 3. Deploy EC2 Instances

- Launch instances with Auto Scaling and attach security groups.
- Use EC2 Instance Connect Endpoint for secure access.

### 4. Configure Database (RDS)

- Set up MySQL RDS instance with private subnet access.
- Attach the appropriate security group.

### 5. Set Up Elastic File System (EFS)

- Configure network access and security settings.

### 6. Deploy Application Load Balancer (ALB)

- Create a Target Group and register EC2 instances.
- Configure listener rules and health checks.

### 7. Set Up Route 53 & SSL

- Register a domain and configure DNS records.
- Use AWS Certificate Manager for SSL/TLS.

# Conclusion

This deployment provides a highly available, scalable, and secure WordPress environment using AWS best practices. Future improvements may include additional monitoring, automated backups, and enhanced security configurations.
