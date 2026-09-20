# Security Notes

## What

Practices this project follows to avoid leaking secrets or state through version control.

## Why

Terraform state and `.tfvars` files can contain sensitive data (resource attributes, secrets passed as variables); committing them to a public or shared repo is a common source of credential leaks.

## How

- Terraform state files (`*.tfstate`, `*.tfstate.*`) are excluded from Git via `.gitignore`.
- Variable files such as `terraform.tfvars`, `*.auto.tfvars`, and `*.tfvars.json` are excluded to prevent accidental secret exposure.
- AWS credentials must never be stored in the repository — configure them locally via the AWS CLI or environment variables instead.
- The EC2 AMI is resolved dynamically through AWS Systems Manager Parameter Store (`data.aws_ssm_parameter.al2023` in [`modules/ec2-web/main.tf`](../modules/ec2-web/main.tf)) instead of being hard-coded, so it always tracks the latest Amazon Linux 2023 image.
- CLI configuration files (`.terraformrc`, `terraform.rc`), which can hold credentials or plugin cache settings, are also excluded.
