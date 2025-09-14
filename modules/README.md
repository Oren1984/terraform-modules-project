# Terraform Modules (Overview)

This folder contains three minimal, modules designed to be combined into a simple stack (VPC + EC2 + S3). Each module avoids hard-coding by exposing inputs and returning outputs for composition.

## s3-basic
- Creates an S3 bucket with Versioning + Lifecycle to Glacier IR (days configurable).
- Randomized bucket name suffix (no hard-coded names).


## vpc-basic
- Minimal VPC with one **public** subnet, Internet Gateway, route table + default route.
- Public IPs on launch for instances in the subnet.


## ec2-web
- Small EC2 instance (default `t3.micro`) with Nginx “Hello” page (user_data).
- AMI via SSM Parameter (AL2023) by default; optional CPU >70% CloudWatch alarm.
- Optional built-in SG (HTTP/HTTPS in, all egress) or attach your own SGs.


## How they fit together
- `vpc-basic` → provides `public_subnet_id`.
- `ec2-web` → uses that subnet; exposes `public_ip`.
- `s3-basic` → independent storage bucket for artifacts/logs/etc.
