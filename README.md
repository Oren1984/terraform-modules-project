# terraform-modules-project

# Terraform Modules Project — S3 + VPC + EC2 (Minimal)

A small, junior-friendly Terraform project with **three minimal modules** combined into one stack:
- **s3-basic** — S3 bucket with Versioning + Lifecycle to Glacier IR (days configurable).
- **vpc-basic** — Minimal VPC with one public subnet, IGW, route table + default route.
- **ec2-web** — Small EC2 (AL2023 via SSM), Nginx “Hello” page, optional CPU>70% CloudWatch alarm.

No hard-coding: AMI via SSM Parameter, CIDRs and tags as inputs, randomized S3 suffix, outputs for composition.

---

## Quick Start

Prereqs:
- Terraform (>= 1.5 recommended)
- AWS account & credentials configured (e.g., `aws configure`)
- Region set in `providers.tf` or via env var `AWS_REGION`

Run from repo root:
```bash
terraform fmt -recursive
terraform init
terraform validate
terraform plan -out=tfplan.bin
terraform apply tfplan.bin


Outputs you’ll see:

vpc_id, public_subnet_id

ec2_instance_id, ec2_public_ip, ec2_sg_id, ec2_cpu_alarm

s3_bucket_name

Clean up:

terraform destroy -auto-approve


How it’s structured
.
├─ main.tf              # final stack: VPC + EC2 + S3
├─ providers.tf         # AWS provider + region
├─ versions.tf          # terraform & provider version constraints
├─ variables.tf         # shared inputs (e.g., tags)
├─ outputs.tf           # (few root outputs; most are module outputs)
└─ modules/
   ├─ s3-basic/         # S3 module (minimal)
   │  ├─ main.tf variables.tf outputs.tf README.md
   │ 
   ├─ vpc-basic/        # VPC module (minimal public-only)
   │  ├─ main.tf variables.tf outputs.tf README.md
   │  
   └─ ec2-web/          # EC2 + Nginx + optional CPU alarm
      ├─ main.tf variables.tf outputs.tf README.md
      


Each module keeps inputs small and outputs useful so modules compose easily in main.tf.


Notes

No hard-coding: AMI via SSM, CIDRs/tags via variables.

State/plan files are ignored by git (see .gitignore).
