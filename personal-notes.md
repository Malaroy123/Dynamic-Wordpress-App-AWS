I sometimes write a personal note when working on my project. So this is a sneak peak for your view. 

# VPC Creation in EU-West-1

## CIDR Block and Tenancy
- **CIDR Block**: 10.0.0.0/16
- **Tenancy**: Default by AWS

## Subnets

### EU-West-1a
- **Public Subnet**
- **Private App Subnet**
- **Private Data Subnet**

### EU-West-1b
- **Public Subnet**
- **Private App Subnet**
- **Private Data Subnet**

## Auto-assign Public IPv4
- Enable auto-assign public IPv4 address for the Public Subnets in AZ1 & AZ2.

# Route Tables

## Create Public Route Table
- Add route to connect to the internet using the **IGW** with destination `0.0.0.0/0`.
- Edit subnet association to associate the **public subnet** with the **public route**.

# NAT Gateway

## Create NAT Gateway
- Create a NAT Gateway in the VPC to allow resources in private subnets to access the internet.
- Add a route to the private route table via the NAT Gateway.
- Associate private subnets with the private route table.

# WordPress Security Groups (SGs)

## 1. ALB SG
- **Description**: Allows inbound HTTP (port 80) and HTTPS (port 443) traffic from the internet to the ALB and permits outbound traffic to any destination.
- **Inbound Rules**:
  - **HTTP (port 80)**: Source: Anywhere (CIDR block: `0.0.0.0/0`)
  - **HTTPS (port 443)**: Source: Anywhere (CIDR block: `0.0.0.0/0`)
- **Outbound Rule**: Default - All traffic, Anywhere.

## 2. EICE SG
- **Description**: Allows outbound SSH (port 22) traffic from the EC2 Instance Connect Endpoint to EC2 instances within the VPC, with no inbound traffic permitted.
- **Outbound Rule**:
  - **SSH (port 22)**: Destination: CIDR Block of VPC (`10.0.0.0/16`)

## 3. App Server SG
- **Description**: Allows traffic on ports 80 and 443 from ALB, and port 22 from EICE SG.
- **Inbound Rules**:
  - **HTTP (port 80)**: Source: ALB SG
  - **HTTPS (port 443)**: Source: ALB SG
  - **SSH (port 22)**: Source: EICE SG
- **Outbound Rule**: Default - All traffic, Anywhere.

## 4. Database SG
- **Description**: Allows inbound traffic on port 3306 (MySQL) only from the App Server SG.
- **Inbound Rules**:
  - **MySQL (port 3306)**: Source: App Server SG
- **Outbound Rule**: Default - All traffic, Anywhere.

## 5. EFS SG
- **Description**: Allows inbound traffic on port 2049 (NFS) from the App Server SG, and port 22 (SSH) from EICE SG.
- **Inbound Rules**:
  - **NFS (port 2049)**: Source: App Server SG
  - **SSH (port 22)**: Source: EICE SG
- **Outbound Rule**: Default - All traffic, Anywhere.

# EC2 Instance Connect Endpoint (EICE)

- **Create EICE**: Create this in the VPC to allow SSH access to resources in public or private subnets without managing SSH keys.
- **Configuration**:
  - **Name**: EC2 Instance Connect Endpoint
  - **VPC**: Select your VPC
  - **SG**: EICE SG
  - **Subnet**: Select any private subnet

> EICE is created in a private subnet for both security and operational efficiency.

# EC2 Instance

- **Test EC2 Instance**: It is often created with the **App Server SG** to test **EICE** and ensure SSH access works properly.

# Interview Questions

- Understand Public & Private Route Tables
- Understand NAT Gateway Overview

# Create Elastic File System (EFS)

1. **Network Access**:
   - Select your VPC
   - Select Availability Zones: EU-West-1a and EU-West-1b
   - **Subnet**: Private Data Subnet
   - **Security Group**: EFS SG

2. Leave other settings as default and create the EFS.

# Create RDS (Relational Database Service)

1. **Create Subnet Group**:
   - **Name**: DB Subnet Group
   - **Description**: Select VPC
   - **Add Subnet**: Availability Zones - EU-West-1a & EU-West-1b
   - **Subnets**: Select Private Data Subnet
   - **Create**

2. **Create RDS DB**:
   - **Engine**: MySQL
   - **Version**: Latest version
   - **Template**: Dev/Test
   - **Instance Identifier**: Name your instance
   - **Instance Class**: Burstable Classes (db.t3.micro)
   - **Storage**: Default
   - **Connectivity**: Select Dev VPC
   - **DB Subnet Group**: Select DB Subnet Group
   - **VPC Security Group**: Database SG
   - **Availability Zone**: EU-West-1b
   - **Database Authentication**: Password Authentication
   - **Username**: Admin
   - **Password**: 1234567890

# Create Application Load Balancer (ALB)

1. **Create Target Group** (TG)
2. **Launch EC2**:
   - **Key Pair**: Proceed without key pair
   - **Network Settings**: Select VPC, Select Private Subnet
   - **SG**: App Server SG
   - **Storage**: Default
   - **Review and Create**

3. **Add EC2 to TG**:
   - Select TG, Register Targets, and add EC2 instance.
   - **Review** and **Register** pending targets.

4. **Create ALB**:
   - **Name**: Enter ALB name
   - **Scheme**: Internet-facing
   - **IP Address Type**: IPv4
   - **Network Mapping**: Select VPC, Select Availability Zones (EU-West-1a & EU-West-1b), Select Public Subnets
   - **SG**: ALB SG
   - **Listener**: HTTP (port 80)
   - **Target Group**: Dev-TG
   - **Review and Create**

