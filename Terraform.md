
# AWS Infrastructure Setup with Terraform

This Terraform configuration defines the infrastructure resources required to set up a basic server environment on AWS. The setup includes the creation of a Virtual Private Cloud (VPC), subnets, routing tables, internet gateway, security groups, and EC2 instances, primarily for use with Jenkins, worker, and Ansible setups.

## Table of Contents
- [Provider Configuration](#provider-configuration)
- [Resources](#resources)
  - [VPC](#vpc)
  - [Subnets](#subnets)
  - [Internet Gateway](#internet-gateway)
  - [Route Table](#route-table)
  - [Security Group](#security-group)
  - [EC2 Instances](#ec2-instances)
- [Usage](#usage)
- [Requirements](#requirements)
- [License](#license)

---

## Provider Configuration

The configuration uses AWS as the provider and sets the region to `us-east-1`. Ensure you have valid AWS credentials set up to apply this configuration.

```hcl
provider "aws" {
    region = "us-east-1"
}

The AWS provider is configured to use the us-east-1 region for all resources.
