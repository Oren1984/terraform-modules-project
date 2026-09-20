# Architecture

## What

The root module composes three independent, reusable modules into a small AWS stack:

```text
                 ┌──────────────────┐
                 │   vpc-basic      │
                 │  VPC + public    │
                 │  subnet + IGW    │
                 └────────┬─────────┘
                          │ public_subnet_id
                          ▼
                 ┌──────────────────┐
                 │    ec2-web       │
                 │ EC2 + SG +       │
                 │ CloudWatch alarm │
                 └──────────────────┘

                 ┌──────────────────┐
                 │    s3-basic      │
                 │ Bucket + version │
                 │ + lifecycle      │
                 └──────────────────┘   (independent, no dependency on vpc/ec2)
```

- **`modules/vpc-basic`** provisions a minimal VPC with one public subnet, an Internet Gateway, and the route table needed for outbound internet access. See [`modules/vpc-basic/README.md`](../modules/vpc-basic/README.md).
- **`modules/ec2-web`** launches a small EC2 instance (default `t3.micro`) running Nginx, resolving its AMI dynamically via SSM (Amazon Linux 2023) instead of hard-coding one, with an optional security group and CloudWatch CPU alarm. See [`modules/ec2-web/README.md`](../modules/ec2-web/README.md).
- **`modules/s3-basic`** creates a standalone S3 bucket with versioning and a lifecycle rule that transitions objects to Glacier Instant Retrieval. It has no dependency on the network stack. See [`modules/s3-basic/README.md`](../modules/s3-basic/README.md).
- [`modules/README.md`](../modules/README.md) gives a one-page summary of all three and how they fit together.

## Why

The stack is split into modules so each piece (network, compute, storage) can be reasoned about, tested, and reused independently — a module never assumes another module's internals, only its declared outputs (e.g. `ec2-web` only needs a `subnet_id`, it doesn't care how that subnet was created).

## How

The root module wires the pieces together in [`main.tf`](../main.tf):

1. `module.vpc` creates the network and exposes `public_subnet_id`.
2. `module.ec2` consumes `module.vpc.public_subnet_id` to place the instance in the public subnet.
3. `module.s3` runs independently, parameterized only by root variables (`var.project_tags`).

Root-level outputs (`vpc_id`, `public_subnet_id`, `ec2_instance_id`, `ec2_public_ip`, `ec2_cpu_alarm`, `s3_bucket_name`) are defined directly in `main.tf` for visibility alongside the module blocks that produce them; `outputs.tf` is reserved for future root-level outputs that aren't tied to a specific module block. Shared configuration lives in `variables.tf` (tags) and `providers.tf` (AWS region, default `eu-north-1`); provider/version constraints are pinned in `versions.tf`.
