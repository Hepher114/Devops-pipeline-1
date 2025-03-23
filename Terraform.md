
# AWS Infrastructure Setup with Terraform

This Terraform configuration defines the infrastructure resources required to set up the environment on AWS. The setup includes the creation of a Virtual Private Cloud (VPC), subnets, routing tables, internet gateway, security groups, and EC2 instances, primarily for use with Jenkins, worker, and Ansible setups.

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

```
provider "aws" {
    region = "us-east-1"
}

```
The AWS provider is configured to use the us-east-1 region for all resources.
# Resources

## VPC
The VPC resource defines a private network with the CIDR block `10.1.0.0/16` and is named `server-vpc`.

```
resource "aws_vpc" "server-vpc" {
  cidr_block = "10.1.0.0/16"
  tags = {
    Name = "server-vpc"
  }
}
```
Subnets
Two public subnets are created across two availability zones (us-east-1a and us-east-1b):

server-public-subnet-01 in us-east-1a

server-public-subnet-02 in us-east-1b
