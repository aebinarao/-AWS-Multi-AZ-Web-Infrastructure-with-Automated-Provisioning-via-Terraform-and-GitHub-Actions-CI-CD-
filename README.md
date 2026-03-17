# AWS Multi-AZ Web-Infrastructure with Automated Provisioning via Terraform and GitHub-ActionsCI-CD-
A production-ready, multi-AZ AWS infrastructure for hosting a static website, provisioned entirely with Terraform and automated via GitHub Actions CI/CD. Features CloudFront CDN, ALB load balancing, Auto Scaling, Bastion Hosts, S3 static asset delivery, and CloudWatch monitoring across two availability zones.


Table of Contents
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




