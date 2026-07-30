# Terraform Modules Project

Modular Terraform project for provisioning a small AWS environment using reusable infrastructure modules.

## Overview

This project demonstrates a clean and reusable Terraform structure built from three independent AWS modules:

- S3
- VPC
- EC2

The root configuration connects the modules and exposes the main infrastructure outputs.

## Architecture

The project provisions:

- An S3 bucket with versioning and lifecycle configuration
- A minimal VPC with a public subnet
- An EC2 instance running an Nginx demonstration page
- A security group for the EC2 instance
- An optional CloudWatch CPU alarm

## Project Structure

```text
terraform-modules-project/
├── modules/
│   ├── s3/
│   ├── vpc/
│   └── ec2/
├── main.tf
├── providers.tf
├── variables.tf
├── outputs.tf
├── versions.tf
├── .terraform.lock.hcl
└── README.md
```
---

## Tech Stack
- Terraform
- AWS
- Amazon EC2
- Amazon VPC
- Amazon S3
- Amazon CloudWatch

---

## Prerequisites

Before running the project, make sure the following are available:

- Terraform installed
- AWS CLI installed
- AWS credentials configured locally
- An AWS account with the required permissions

Verify AWS access:

```bash
aws sts get-caller-identity
```

---

## Usage

Format and initialize the project:

```bash
terraform fmt -recursive
terraform init
```

Validate the configuration:

```bash
terraform validate
```

Create an execution plan:

```bash
terraform plan -out=tfplan.bin
```

Apply the saved plan:

```bash
terraform apply tfplan.bin
``

---

## Outputs

The root module exposes outputs such as:

- vpc_id
- public_subnet_id
- ec2_instance_id
- ec2_public_ip
- ec2_sg_id
- ec2_cpu_alarm
- s3_bucket_name

---

## Cleanup

Destroy all infrastructure created by the project:

```bash
terraform destroy -auto-approve
```

---

## Security Notes

- Terraform state files are excluded from Git.
- Variable files such as terraform.tfvars are excluded to prevent accidental secret exposure.
- AWS credentials must not be stored in the repository.
- The AMI is resolved dynamically through AWS Systems Manager Parameter Store instead of being hard-coded.

---

## Purpose

This project was created as a hands-on Terraform portfolio project demonstrating:

- Reusable module design
- Root-module composition
- Input variables and outputs
- AWS provider configuration
- Infrastructure validation
- Safe Git repository practices

---

