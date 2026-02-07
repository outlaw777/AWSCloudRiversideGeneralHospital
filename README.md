

# **AWS Riverside General Hospital VPC Architecture**

## **Overview**
This repository documents the design and deployment of the **Riverside General Hospital (RGH) Production VPC**, a secure, highly available, and compliance‑aligned cloud network built on AWS. The architecture supports mission‑critical healthcare workloads including **EHR systems, imaging platforms (PACS/DICOM), clinical APIs, and backend data services**, while enforcing strict segmentation, auditability, and zero‑trust access principles.

The VPC is engineered to meet the operational, regulatory, and security demands of a modern healthcare organization. It incorporates **multi‑AZ resiliency**, **tiered subnet isolation**, **defense‑in‑depth controls**, and **AWS-native security services** to ensure confidentiality, integrity, and availability of protected health information (PHI).

---

<img width="1866" height="1184" alt="Screenshot 2026-02-06 133953" src="https://github.com/user-attachments/assets/d4cebec3-f30d-4dd1-b63f-ee5e6b0d7568" />


# Architecture Summary

## **VPC**
- **CIDR:** `10.0.0.0/16`  
- Designed as a **production‑grade, multi‑AZ healthcare network** supporting clinical, imaging, and data workloads.

## **Subnet Layout**
| Subnet Type | CIDR Examples | Purpose |
|-------------|---------------|---------|
| **Public Subnets** | `10.0.1.0/24`, `10.0.2.0/24` | ALBs, Bastion, NAT Gateways, VPN |
| **App Subnets** | `10.0.11.0/24`, `10.0.12.0/24` | EHR frontends, APIs, microservices |
| **Data Subnets** | `10.0.21.0/24`, `10.0.22.0/24` | RDS, Redis, backend data stores |
| **Imaging Subnets** | `10.0.31.0/24`, `10.0.32.0/24` | PACS, DICOM services, imaging archive |
| **Ops Subnet** | `10.0.41.0/24` | Monitoring, scanners, operational tooling |

## **Routing**
- Public subnets → **Internet Gateway**
- App subnets → **NAT Gateways (per AZ)**
- Data subnets → **VPC Endpoints only (no internet path)**
- Imaging subnets → **S3 VPC Endpoint for archive storage**

## **Security Layers**
- **Security Groups:** Workload‑specific, least‑privilege rules  
- **NACLs:** Tiered subnet‑level filtering (public, app, data, imaging)  
- **AWS Network Firewall:** IDS/IPS and egress filtering  
- **Route 53 Resolver:** DNS filtering + inbound/outbound endpoints  
- **VPC Endpoints:** S3, CloudWatch, KMS, Backup, DynamoDB  

## **Access Model**
- **Bastion host** for controlled administrative access  
- **SSM Session Manager** for keyless, auditable access  
- **No public IPs** on app, data, or imaging workloads  

---

# **Why This Architecture Was Chosen**

This section explains the *business, compliance, and engineering rationale* behind the RGH VPC design.

---

## **A. Security Leadership Perspective (CISO / CIO / C‑Suite)**

### **Regulatory Alignment**
- Architecture mirrors segmentation patterns used in **HIPAA, HITRUST, and DoD** environments.
- Ensures PHI remains in private, tightly controlled subnets.
- Full auditability through CloudTrail, VPC Flow Logs, and IAM least privilege.

### **Risk Reduction**
- No clinical or data systems are internet‑facing.
- All ingress is funneled through hardened ALBs and WAF.
- Network Firewall and DNS Firewall enforce zero‑trust egress.

### **Operational Resilience**
- Multi‑AZ design ensures high availability for clinical systems.
- Redundant NAT Gateways, ALBs, and subnets prevent single points of failure.

### **Strategic Value**
- Provides a scalable foundation for EHR modernization, imaging expansion, and API‑driven interoperability.

---

## **Engineering Leadership Perspective (Principal Engineers / Architects)**

### **Clean, Tiered Segmentation**
- Public → App → Data → Imaging → Ops  
- Each tier has dedicated NACLs, route tables, and security groups.

### **Enterprise‑Grade Patterns**
- Multi‑AZ load balancing  
- Private RDS with KMS encryption  
- S3 VPC endpoint for imaging archives  
- Network Firewall for IDS/IPS  

### **Infrastructure as Code**
- Terraform ensures reproducibility, version control, and rapid iteration.

### **Extensibility**
- New clinical services or microservices can be added without redesigning the network.

---

## **Manager Perspective (Team Leads / Project Managers)**

### **Predictable, Documented Environment**
- Clear diagrams, Terraform modules, and deployment workflows.
- Easy onboarding for new engineers and auditors.

### **Supports Multiple Hospital Use Cases**
- EHR hosting  
- Imaging workflows  
- API integrations  
- Data analytics  
- Monitoring and compliance  

### **Reduces Vendor Lock‑In**
- Built entirely on AWS-native services and open standards.

---

# **Core Services Deployed**

## **Application Layer**
- EHR frontends  
- Clinical APIs  
- Microservices  

## **Data Layer**
- RDS (multi‑AZ)  
- Redis  
- Encrypted backups via AWS Backup  

## **Imaging Layer**
- PACS  
- DICOM services  
- S3 imaging archive  

## **Operational Layer**
- Monitoring  
- Scanners  
- Logging pipelines  

---

# **Deployment Workflow (Terraform)**

## **Network Foundation**
- VPC, subnets, route tables  
- IGW, NAT Gateways  
- NACLs and security groups  

## **Security & Compliance**
- Network Firewall  
- Route 53 Resolver endpoints  
- VPC Flow Logs, CloudTrail, CloudWatch  

## **Application & Data Services**
- ALBs  
- EC2 workloads  
- RDS and Redis  
- S3 endpoints  

---

# **Benefits to the Organization**

## **Security**
- Strong segmentation protects PHI  
- Zero‑trust egress and DNS filtering  
- Full auditability  

## **Operational**
- Highly available, multi‑AZ design  
- Automated deployment and updates  
- Consistent environment for all teams  

## **Strategic**
- Supports modernization initiatives  
- Scales with hospital growth  
- Reduces reliance on legacy on‑prem systems  

---

# **Future Enhancements**
- Multi‑Region DR  
- Automated compliance reporting  
- SIEM integration  
- Containerized clinical services  
- API gateway modernization  

---

# **Conclusion**

The Riverside General Hospital VPC architecture delivers a **secure, compliant, and highly available foundation** for modern healthcare workloads. It reflects industry best practices and positions the organization for long‑term scalability, operational excellence, and regulatory readiness.

---

Just tell me what direction you want to take next.
