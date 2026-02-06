
# **AWS Cloud – Riverside General Hospital (RGH) VPC Architecture**

This repository showcases a secure and scalable AWS Virtual Private Cloud (VPC) architecture for **Riverside General Hospital**. It demonstrates best practices for subnet segmentation, access control, and integration with core AWS services aligned with HIPAA‑ready design principles.

---


<img width="1866" height="1184" alt="Screenshot 2026-02-06 133953" src="https://github.com/user-attachments/assets/9b7f30be-93f7-46e0-a82e-5c8229676a08" />

---
## **Region**
- **AWS Region:** `us-east-1`
- **Availability Zones:** `us-east-1a`, `us-east-1b`
- **VPC Name:** `rgh-prod-vpc`
- **CIDR:** `10.20.0.0/16`
- **Tags:**  
  - `Environment=Prod`  
  - `Org=RiversideGeneralHospital`  
  - `Compliance=HIPAA`

---

# **Public Subnets**

| Subnet Name     | CIDR          | AZ         | Purpose                               |
|-----------------|---------------|------------|----------------------------------------|
| rgh-public-a    | 10.20.1.0/24  | us-east-1a | Bastion, NAT, ALB, VPN                 |
| rgh-public-b    | 10.20.2.0/24  | us-east-1b | Redundant NAT, ALB                     |

### **Routing**
- **Route Table:** `rtb-rgh-public`  
  - `0.0.0.0/0 → IGW`
- **Internet Gateway:** `igw-rgh-prod`

### **Network ACL**
- Inbound: Allow `tcp/443`
- Outbound: Allow ephemeral ports

### **Security Groups**
- **sg-rgh-bastion:** SSH from home ESXi lab  
- **sg-rgh-web-alb:** HTTPS from internet or WAF

---

# **Private Subnets**

## **App Subnets (EHR, APIs, Microservices)**

| Subnet Name  | CIDR           | AZ         |
|--------------|----------------|------------|
| rgh-app-a    | 10.20.11.0/24  | us-east-1a |
| rgh-app-b    | 10.20.12.0/24  | us-east-1b |

### **Routing**
- **Route Table:** `rtb-rgh-app → NAT Gateway`

### **NACL**
- Allow HTTPS from ALB  
- Allow DB access to data subnets  

### **Security Group: `sg-rgh-app-servers`**
- **Inbound:** HTTPS from ALB, SSH from bastion  
- **Outbound:** PostgreSQL to RDS, Redis, HTTPS to external APIs  

---

## **Data Subnets (RDS, Redis)**

| Subnet Name  | CIDR           | AZ         |
|--------------|----------------|------------|
| rgh-data-a   | 10.20.21.0/24  | us-east-1a |
| rgh-data-b   | 10.20.22.0/24  | us-east-1b |

### **Routing**
- **Route Table:** `rtb-rgh-data → VPC Endpoints only`

### **NACL**
- Allow DB ports from app  
- Deny public access  

### **Security Group: `sg-rgh-rds`**
- **Inbound:** PostgreSQL from app servers  
- **Outbound:** Backup, monitoring, KMS only  

---

## **Imaging Subnets (PACS, DICOM)**

| Subnet Name     | CIDR           | AZ         |
|------------------|----------------|------------|
| rgh-imaging-a    | 10.20.31.0/24  | us-east-1a |
| rgh-imaging-b    | 10.20.32.0/24  | us-east-1b |

### **Routing**
- **Route Table:** `rtb-rgh-imaging → S3 VPC Endpoint`

### **Security Group: `sg-rgh-pacs`**
- **Inbound:** DICOM from app + home imaging gateway  
- **Outbound:** S3 VPC endpoint for archiving  

---

## **Ops Subnet (Monitoring, Scanners)**

| Subnet Name | CIDR           | AZ         |
|-------------|----------------|------------|
| rgh-ops-a   | 10.20.41.0/24  | us-east-1a |

---

# **Security Services**

- **VPC Flow Logs:** → CloudWatch Logs (`rgh-prod-vpc-flowlogs`)
- **AWS Network Firewall:** Egress filtering, IDS/IPS
- **Route 53 Resolver:** Inbound/outbound endpoints, DNS firewall
- **CloudTrail:** Logs to `rgh-org-cloudtrail-logs`
- **CloudWatch:** Alarms for login failures, outbound anomalies
- **Security Hub & GuardDuty:** Continuous threat detection

---
