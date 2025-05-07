# 🌐 AWS VPC Setup: Public and Private Subnets Across Two Availability Zones

## Overview

This guide walks you through creating an AWS VPC infrastructure with high availability and logical separation of public and private resources across two Availability Zones (AZs). The architecture includes:

- A single VPC.
- Two Availability Zones: Zone A and Zone B.
- Each AZ with:
  - One Public Subnet
  - One Private Subnet
- Internet access for public subnets via an Internet Gateway (IGW).
- Communication between private subnets via a Virtual Private Gateway (VPG).

---

## 📘 Definitions

- **VPC (Virtual Private Cloud)**: An isolated AWS network to host your resources.
- **Subnet**: A range of IP addresses in your VPC (can be public or private).
- **Internet Gateway (IGW)**: Enables public internet access for your VPC.
- **Virtual Private Gateway (VPG)**: Allows secure private communication, typically used for VPNs or other VPCs.
- **Route Table**: Determines how network traffic is directed.
- **Availability Zone (AZ)**: A distinct physical location within an AWS region for fault tolerance.

---

## 🏗️ Architecture

### VPC

- **CIDR Block**: `10.0.0.0/24`

### Subnet Allocation

| Subnet              | AZ         | CIDR Block       |
|---------------------|------------|------------------|
| Public Subnet 1     | Zone A     | 10.0.0.0/26      |
| Private Subnet 1    | Zone A     | 10.0.0.128/26    |
| Public Subnet 2     | Zone B     | 10.0.0.64/26     |
| Private Subnet 2    | Zone B     | 10.0.0.192/26    |

### Gateways

- **IGW**: Enables internet for public subnets.
- **VPG**: Enables communication between private subnets.

---

## 🔧 Setup Instructions

### Step 1: Create the VPC

1. Go to **VPC Dashboard** → **Create VPC**
2. **Name**: `My-VPC`
3. **CIDR Block**: `10.0.0.0/24`
4. Click **Create VPC**

### Step 2: Create Subnets

#### Zone A

- **Public-Subnet-1**: `10.0.0.0/26` in `us-east-1a`
- **Private-Subnet-1**: `10.0.0.128/26` in `us-east-1a`

#### Zone B

- **Public-Subnet-2**: `10.0.0.64/26` in `us-east-1b`
- **Private-Subnet-2**: `10.0.0.192/26` in `us-east-1b`

### Step 3: Create and Attach an Internet Gateway (IGW)

1. Navigate to **Internet Gateways**
2. Click **Create Internet Gateway** → Name: `My-IGW`
3. Attach to `My-VPC`

### Step 4: Create and Attach a Virtual Private Gateway (VPG)

1. Navigate to **Virtual Private Gateways**
2. Click **Create Virtual Private Gateway** → Name: `My-VPG`
3. Attach to `My-VPC`

### Step 5: Configure Route Tables

#### Public Route Table

1. Create Route Table → Name: `Public-Route-Table` → VPC: `My-VPC`
2. Add route: `0.0.0.0/0` → Target: `My-IGW`
3. Associate with: `Public-Subnet-1` and `Public-Subnet-2`

#### Private Route Table

1. Create Route Table → Name: `Private-Route-Table`
2. Enable Route Propagation: Select `My-VPG`
3. Associate with: `Private-Subnet-1` and `Private-Subnet-2`

---

## 🚀 Launch EC2 Instances

### In Public Subnets

Launch EC2 instances in `Public-Subnet-1` and `Public-Subnet-2` with public IP enabled.

### In Private Subnets

1. Launch EC2 in `Private-Subnet-1` and `Private-Subnet-2`
2. Disable public IP during setup
3. Use security groups to allow SSH access from known IP addresses

---

## 🔐 Configure Security Group for SSH Access

1. Go to **Security Groups**
2. Create new group: `Private-SSH-Access`
3. Add inbound rule:
   - **Type**: SSH
   - **Port**: 22
   - **Source**: Your IP or custom CIDR (e.g., `203.0.113.25/32`)
4. Attach to private EC2 instances

---

## ✅ Testing & Verification

- **Public Subnets**: Confirm internet access via public IP.
- **Private Subnets**: Ensure secure internal connectivity (e.g., ping/SSH between private instances).
- Use appropriate tools (e.g., `curl`, `ping`, `ssh`) to test routes.

---

## 🧪 Optional Enhancements

- NAT Gateway for internet access in private subnets
- VPN connection with on-premise data centers
- Network ACLs for tighter security
- Flow Logs for traffic analysis

---

## 📄 License

MIT License — Use this template freely for infrastructure projects.

---

## 🧭 Author

Created by [Your Name or Org] — Contributions welcome!



Useful Links

https://docs.aws.amazon.com/vpc/latest/userguide/

https://docs.aws.amazon.com/ec2/latest/userguide/

https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html

https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html

