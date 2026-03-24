# AWS Multi-AZ Web-Infrastructure with Automated Provisioning via Terraform and GitHub-Actions CI-CD
A production-ready, multi-AZ AWS infrastructure for hosting a static website, provisioned entirely with Terraform and automated via GitHub Actions CI/CD. Features CloudFront CDN, ALB load balancing, Auto Scaling, Bastion Hosts, S3 static asset delivery, and CloudWatch monitoring across two availability zones.


# Table of Contents
* Overview
* Architecture
* Features
* Technology Stack
* Prerequisites
* Getting Started
* Deployment
* Testing
* Cost Analysis
* Security
* Monitoring
* Troubleshooting

# Overview
This project demonstrates enterprise-grade cloud infrastructure design and deployment on AWS. It implements a highly available, auto-scaling web application architecture with global content delivery, following AWS Well-Architected Framework best practices.

# Architecture
<p align="center">
  <img src="/images/aws-vpc-project.png" width="1000" />
</p>

# Network Topology
## Traffic Flow
### Administrator Access Flow
<p>&nbsp;&nbsp;&nbsp;
Administrator<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↓<br>&nbsp;&nbsp;
SSH to Bastion Host (Public Subnet)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↓<br>&nbsp;&nbsp;
SSH to Web Application Instance (Private Subnet)
</p>

### End User Request Flow
<p>&nbsp;&nbsp;&nbsp;
End User<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↓<br>&nbsp;&nbsp;
CloudFront (Global CDN - Edge Caching)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↓<br>&nbsp;&nbsp;
Application Load Balancer (Multi-AZ)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↓<br>&nbsp;&nbsp;
Target Group (Health Checked)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↓<br>&nbsp;&nbsp;
Auto Scaling Group (2-6 instances)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↓<br>&nbsp;&nbsp;
Web Application Instances (Private Subnets)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↓<br>&nbsp;&nbsp;
S3 Bucket (via Gateway Endpoint) OR Internet (via NAT Gateway)
</p>

### Developer Deployment Flow
<p>&nbsp;&nbsp;&nbsp;
Developer<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↓<br>&nbsp;&nbsp;
Git Push to GitHub<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↓<br>&nbsp;&nbsp;
GitHub Actions (CI/CD Pipeline)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↓<br>&nbsp;&nbsp;
Terraform Validation & Planning<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↓<br>&nbsp;&nbsp;
AWS API (Infrastructure Provisioning)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↓<br>&nbsp;&nbsp;
VPC, Subnets, ALB, ASG, CloudFront Created/Updated
</p>

# Features
## Networking & Connectivity
   * Multi-AZ VPC Design: Spans 2 availability zones for fault tolerance
   * Public/Private Subnet Segmentation: DMZ pattern for security isolation
   * Internet Gateway: Managed route to the internet for public resources
   * NAT Gateway: Secure outbound internet access for private instances
   * VPC Gateway Endpoints: Direct S3 access bypassing NAT (cost optimization)

## Load Balancing & Auto Scaling
   * Application Load Balancer: Layer 7 load balancing across multiple AZs
   * Target Groups: Health-checked instance registration
   * Auto Scaling Groups: CPU-based scaling (2 min, 6 max instances)
   * Launch Templates: Standardized instance configuration
   * Self-Healing: Automatic replacement of unhealthy instances

## Content Delivery & Caching
   * CloudFront Distribution: Global CDN with edge caching
   * Custom Origin: ALB as CloudFront origin
   * Cache Policies: Optimized TTL for static and dynamic content
   * SSL/TLS: HTTPS support with AWS Certificate Manager integration

## Security
   * Defense-in-Depth: Multiple security layers
   * Security Groups: Stateful firewall with least-privilege rules
   * Private Subnets: No direct internet access for application servers
   * Bastion Hosts: Controlled administrative access
   * IAM Roles: Instance profiles for AWS service access
   * VPC Flow Logs: Network traffic logging for audit and analysis
   * Encryption: Data at rest (S3, EBS) and in transit (HTTPS)

## Infrastructure as Code
   * Terraform: Complete infrastructure defined as code
   * Version Control: All changes tracked in Git
   * Modules: Reusable, maintainable infrastructure components
   * Variables: Environment-specific configurations
   * State Management: Remote state with locking

## CI/CD & Automation
   * GitHub Actions: Automated testing and deployment
   * Terraform Validation: Syntax and configuration checks
   * Plan on PR: Infrastructure changes visible before merge
   * Auto-Apply: Deployment triggered on main branch merge
   * Rollback Support: Git-based infrastructure rollback

## Monitoring & Observability
   * VPC Flow Logs: Network traffic analysis
   * CloudWatch Metrics: Resource utilization tracking
   * CloudWatch Alarms: Automated scaling triggers
   * Health Checks: ALB and ASG health monitoring
   * Access Logs: ALB request logging

# Technology Stack
## Cloud Provider
  * AWS - Amazon Web Services

## Infrastructure as Code
  * Terraform
  * HashiCorp Configuration Language

## CI/CD 
  * GitHub Actions
  * GitHub for version control

## AWS Services
  * VPC - Virtual Private Cloud
  * EC2 - Elastic Compute Cloud 
  * ALB - Application Load Balancer
  * ASG - Auto Scaling Groups
  * CloudFront - Content Delivery Network
  * S3 - Simple Storage Service
  * IAM - Identity and Access Management
  * CloudWatch - Monitoring and Logging
  * VPC Endpoints - Gateway Endpoints for S3

## Operating System
  * Amazon Linux 2023 - Latest AWS-optimized Linux distribution

# Testing
### Connectivity & Network Tests
  * Internet → IGW → CloudFront → ALB ✅ reachable
  * ALB → Web App in private subnet ✅ reachable
  * Web App → S3 via Gateway Endpoint ✅ (no NAT used)
  * SSH to Bastion Host (public subnet) ✅ works
  * SSH from Bastion → Web App (private subnet) ✅ works
  * Direct SSH to Web App from internet ❌ should be blocked

### Auto Scaling & Resilience Tests
  * Terminate one Web App instance → ASG should launch a replacement
  * ALB health check should remove unhealthy targets automatically
  * Simulate AZ-1A failure → traffic should continue via AZ-1C

### CI/CD Pipeline Tests
  * Push a bad Terraform config → pipeline should fail before apply
  * Verify no secrets/tokens appear in GitHub Actions logs
  * Test that only authorized branches trigger production deploy

# Security Testing Phase
## Network Security
<table>
  <tr>
    <th>Traffic</th>
    <th>Expected Result</th>
  </tr>
   <tr>
    <td>Internet → Bastion (port 22)</td>
    <td>✅ Allowed</td>
  </tr>
  <tr>
    <td>Internet → Web App directly (port 22)</td>
    <td>❌ Blocked</td>
  </tr>
  <tr>
    <td>Internet → ALB (port 80/443)</td>
    <td>✅ Allowed</td>
  </tr>
  <tr>
    <td>ALB → Web App (app port)</td>
    <td>✅ Allowed</td>
  </tr>
  <tr>
    <td>Web App → S3 (via Gateway Endpoint)</td>
    <td>✅ Allowed, no Internet</td>
  </tr>
  <tr>
    <td>Any → Web App (direct, no Bastion)</td>
    <td>❌ Blocked</td>
  </tr>
</table>

## S3 Bucket Security
  * Block Public Access enabled ✅
  * Access only via S3 Gateway Endpoint from private subnet (no public internet path)
  * Bucket policy restricts access to your VPC/endpoint only
  * Versioning enabled (good practice to mention)
  * Server-side encryption (SSE-S3 or SSE-KMS)

## Data in Transit
  * CloudFront should enforce HTTPS only (redirect HTTP → HTTPS)
  * ALB should have an SSL/TLS certificate (ACM)
  * SSH sessions to Bastion use key pairs, not passwords

## Secrets & Credentials Management
  * No hardcoded credentials in your GitHub repo or Terraform files
  * .gitignore should exclude terraform.tfstate, .tfvars, and any *.pem files

## Logging & Auditability
  * VPC Flow Logs enabled (captures all network traffic metadata)
  * S3 access logging enabled
  * CloudWatch alarms for suspicious activity (e.g., repeated SSH failures)


# Cost Analysis

## Compute (EC2 — Bastion Hosts + Web App + Auto Scaling)
  * 2 Bastion Hosts (one per AZ — AZ-1A and AZ-1C)
  * Web App instances in private subnets (managed by ASG)
<table>
  <tr>
    <th>Resource</th>
    <th>Instance Type (Recommended)</th>
    <th>Est. Monthly Cost</th>
  </tr>
   <tr>
    <td>Bastion Host × 2</td>
    <td>t2.micro</td>
    <td>~$8–$8.70</td>
  </tr>
  <tr>
    <td>Web App (ASG min 2)</td>
    <td>t2.micro</td>
    <td>~$8.50 - $12.00</td>
  </tr>
  <tr>
    <td>Total Compute</td>
    <td></td>
    <td>~$16.50 - $20.70</td>
  </tr>
</table>


| Component | Est. Monthly Cost |
|---|---|
| EC2 (Bastion + Web App ASG) | ~$38–$77 |
| Networking (NAT GW + ALB + CloudFront) | ~$85–$135 |
| S3 Storage | ~$1–$2 |
| CloudWatch Observability | ~$3–$13 |
| **Estimated Total** | **~$127–$227/mo** |

> Cost optimizations applied: S3 Gateway Endpoint (avoids NAT charges for S3),
> CloudFront for edge caching, Auto Scaling to right-size compute demand.
  



