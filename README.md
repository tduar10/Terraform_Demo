# Terraform_Demo
This is a small Terraform module for demonstartion puproses

Before using this module, or any module, you'll have to install terraform.

Here is an example main.tf file showing the usage of this module:
```
terraform {
  required_version = ">= 1.0.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = ">= 4.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

data "aws_ami" "latest_amazon_linux" {
  most_recent = true

  filter {
    name   = "name"
    values = ["amzn2-ami-hvm-*-x86_64-gp2"]
  }

  filter {
    name   = "architecture"
    values = ["x86_64"]
  }

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }

  owners = ["amazon"]
}

module "ec2_s3" {
  source        = "./aws-instance-s3-bucket-module"
  aws_region    = "us-east-1"
  ami_id        = data.aws_ami.latest_amazon_linux.id  # Use ami data source
  instance_type = "t2.micro"
  bucket_name   = "demo-bucket-8334734789"
}

output "ec2_public_ip" {
  description = "Public IP of the EC2 instance"
  value       = module.ec2_s3.ec2_public_ip
}

output "s3_bucket_arn" {
  description = "ARN of the S3 bucket"
  value       = module.ec2_s3.s3_bucket_arn
}
```
This module has 5 inputs:
source         = path to module
aws_region     = string #AWS region to deploy resources
instance_type  = string #EC2 instance type
ami_id         = string #AMI ID for the EC2 instance
bucket_name    = string #Name of the S3 bucket, must be unique!

This module has 2 outputs:
ec2_public_ip	 = The public IP address of the EC2 instance
s3_bucket_arn	 = The ARN (Amazon Resource Name) of the S3 bucket

