# Usage

## What

Step-by-step commands to format, validate, plan, apply, and tear down this stack, plus the full list of outputs it exposes.

## Why

Terraform changes to real AWS infrastructure should always go through `plan` before `apply`, and be reviewable ahead of time — these steps make that review possible and repeatable.

## How

### Prerequisites

- Terraform installed (`>= 1.5.0`, see [`versions.tf`](../versions.tf))
- AWS CLI installed and credentials configured locally
- An AWS account/role with permissions for VPC, EC2, S3, and CloudWatch

Verify AWS access before running anything:

```bash
aws sts get-caller-identity
```

### Format and initialize

```bash
terraform fmt -recursive
terraform init
```

### Validate

```bash
terraform validate
```

### Plan and apply

```bash
terraform plan -out=tfplan.bin
terraform apply tfplan.bin
```

### Outputs

After apply, the root module exposes:

| Output | Description |
|---|---|
| `vpc_id` | ID of the created VPC |
| `public_subnet_id` | ID of the public subnet |
| `ec2_instance_id` | ID of the EC2 instance |
| `ec2_public_ip` | Public IP of the EC2 instance |
| `ec2_cpu_alarm` | Name of the CloudWatch CPU alarm |
| `s3_bucket_name` | Name of the created S3 bucket |

### Cleanup

Destroy all infrastructure created by this stack:

```bash
terraform destroy -auto-approve
```
