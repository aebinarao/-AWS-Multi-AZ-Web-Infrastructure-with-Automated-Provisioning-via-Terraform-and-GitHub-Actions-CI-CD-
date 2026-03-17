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
# Traffic Flow
```Administrator Access Flow
Administrator
    ↓
SSH to Bastion Host (Public Subnet)
    ↓
SSH to Web Application Instance (Private Subnet)
```


