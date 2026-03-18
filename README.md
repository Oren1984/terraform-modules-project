# Terraform Modules Project

Minimal Terraform project combining reusable AWS modules for S3, VPC, and EC2.

---

## Overview

A small Terraform lab that demonstrates how to build a simple AWS stack using three modular components: S3, VPC, and EC2.

---

## Tech Stack

- Terraform
- AWS
- EC2
- VPC
- S3
- CloudWatch

---

## Quick Start

```bash
terraform fmt -recursive
terraform init
terraform validate
terraform plan -out=tfplan.bin
terraform apply tfplan.bin
```

---
## Usage

This project provisions:

- S3 bucket with versioning and lifecycle policy

- Minimal VPC with one public subnet

- EC2 instance with Nginx demo page

- Optional CloudWatch CPU alarm

Common outputs:

- vpc_id

- public_subnet_id

- ec2_instance_id

- ec2_public_ip

- ec2_sg_id

- ec2_cpu_alarm

- s3_bucket_name

---

## Cleanup

```bash
terraform destroy -auto-approve
```

---

## Notes

- Built for learning and modular Terraform practice

- Uses reusable modules under modules/

- AMI is resolved via SSM, with no hard-coded values

- Validation was completed locally before deployment

---
