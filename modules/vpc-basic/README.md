# vpc-basic

Minimal VPC for demos. One **public** subnet with Internet access.

## Creates
- VPC (default CIDR via input)
- Internet Gateway (IGW)
- Public subnet
- Route table + default route `0.0.0.0/0` via IGW
- Route table association to the subnet

## Inputs (short)
- `vpc_cidr` (string) — e.g. `10.0.0.0/16`
- `public_subnet_cidr` (string) — e.g. `10.0.1.0/24`
- `tags` (map) — optional common tags

## Outputs
- `vpc_id`
- `public_subnet_id`
- `igw_id`
- `route_table_id`

## Example (root)
```hcl
module "vpc" {
  source             = "./modules/vpc-basic"
  vpc_cidr           = "10.0.0.0/16"
  public_subnet_cidr = "10.0.1.0/24"
  tags               = var.project_tags
}

