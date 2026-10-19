# Terraform Modules Project

Modular Terraform project for provisioning a small AWS environment using reusable infrastructure modules.

## What

This project provisions a small AWS environment from three independent, reusable modules:

- An S3 bucket with versioning and lifecycle configuration
- A minimal VPC with a public subnet
- An EC2 instance running an Nginx demonstration page, with a security group and an optional CloudWatch CPU alarm

The root configuration ([`main.tf`](main.tf)) connects these modules and exposes the resulting infrastructure IDs as outputs.

```mermaid
flowchart TD
    vars["variables.tf<br/>project_tags"] --> root["Root module<br/>main.tf"]
    prov["providers.tf / versions.tf<br/>AWS + random providers"] --> root

    root --> vpc
    root --> ec2
    root --> s3

    subgraph vpc["module.vpc (vpc-basic)"]
        v1["aws_vpc<br/>10.0.0.0/16"] --> v2["aws_subnet (public)<br/>10.0.1.0/24"]
        v1 --> v3["aws_internet_gateway"]
        v3 --> v4["aws_route_table<br/>+ 0.0.0.0/0 route"]
        v4 --> v5["route_table_association"]
        v2 --> v5
    end

    subgraph ec2["module.ec2 (ec2-web)"]
        e1["aws_ssm_parameter<br/>AL2023 AMI"] --> e3["aws_instance<br/>t3.micro + Nginx"]
        e2["aws_security_group"] --> e3
        e3 --> e4["aws_cloudwatch_metric_alarm<br/>CPU > 70%"]
    end

    subgraph s3["module.s3 (s3-basic)"]
        s1["random_pet suffix"] --> s2["aws_s3_bucket<br/>oren-modules-demo-*"]
        s2 --> s3v["versioning"]
        s2 --> s4["lifecycle<br/>Glacier after 30 days"]
    end

    v2 -- "public_subnet_id" --> e3

    vpc --> out["Root outputs<br/>vpc_id, public_subnet_id,<br/>ec2_instance_id, ec2_public_ip,<br/>ec2_cpu_alarm, s3_bucket_name"]
    ec2 --> out
    s3 --> out
```

```text
terraform-modules-project/
├── modules/
│   ├── vpc-basic/
│   ├── ec2-web/
│   └── s3-basic/
├── docs/
│   ├── ARCHITECTURE.md
│   ├── USAGE.md
│   └── SECURITY.md
├── main.tf
├── providers.tf
├── variables.tf
├── outputs.tf
├── versions.tf
├── .terraform.lock.hcl
└── README.md
```

## Why

This project was built as a hands-on Terraform portfolio piece demonstrating:

- Reusable module design (network, compute, and storage kept independent)
- Root-module composition instead of a single monolithic configuration
- Input variables and outputs used for composition, not hard-coded values
- AWS provider configuration and pinned version constraints
- Safe Git practices around state and secrets (see [`docs/SECURITY.md`](docs/SECURITY.md))

## How

**Tech stack:** Terraform, AWS (VPC, EC2, S3, CloudWatch)

**Quick start:**

```bash
terraform fmt -recursive
terraform init
terraform validate
terraform plan -out=tfplan.bin
terraform apply tfplan.bin
```

**Cleanup:**

```bash
terraform destroy -auto-approve
```

See the `docs/` folder for details:

- [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) — what each module does and how they compose
- [`docs/USAGE.md`](docs/USAGE.md) — full command walkthrough, prerequisites, and output reference
- [`docs/SECURITY.md`](docs/SECURITY.md) — how state, secrets, and credentials are kept out of version control

Each module also has its own reference doc: [`modules/README.md`](modules/README.md), [`modules/vpc-basic/README.md`](modules/vpc-basic/README.md), [`modules/ec2-web/README.md`](modules/ec2-web/README.md), [`modules/s3-basic/README.md`](modules/s3-basic/README.md).
