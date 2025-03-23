
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

## Subnets
Two public subnets are created across two availability zones (`us-east-1a` and `us-east-1b`):

1. `server-public-subnet-01` in `us-east-1a`
2. `server-public-subnet-02` in `us-east-1b`

```
resource "aws_subnet" "server-public-subnet-01" {
  vpc_id                  = aws_vpc.server-vpc.id
  cidr_block              = "10.1.1.0/24"
  map_public_ip_on_launch = true
  availability_zone       = "us-east-1a"
  tags = {
    Name = "server-public-subnet-01"
  }
}

resource "aws_subnet" "server-public-subnet-02" {
  vpc_id                  = aws_vpc.server-vpc.id
  cidr_block              = "10.1.2.0/24"
  availability_zone       = "us-east-1b"
  map_public_ip_on_launch = true
  tags = {
    Name = "server-public-subnet-02"
  }
}
```
## Internet Gateway
An Internet Gateway (`server-igw`) is created and attached to the VPC for external internet access.

```
resource "aws_internet_gateway" "server-igw" {
  vpc_id = aws_vpc.server-vpc.id
  tags = {
    Name = "server-igw"
  }
}
```

## Route Table
A route table (`server-public-rt`) is defined, routing all traffic (`0.0.0.0/0`) through the Internet Gateway.

```hcl
resource "aws_route_table" "server-public-rt" {
  vpc_id = aws_vpc.server-vpc.id
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.server-igw.id
  }
}
```
## Route Table Associations
The route table is associated with the public subnets to enable routing.

```hcl
resource "aws_route_table_association" "server-rta-public-subnet-01" {
  subnet_id      = aws_subnet.server-public-subnet-01.id
  route_table_id = aws_route_table.server-public-rt.id
}

resource "aws_route_table_association" "server-rta-public-subnet-02" {
  subnet_id      = aws_subnet.server-public-subnet-02.id
  route_table_id = aws_route_table.server-public-rt.id
}
```
## Security Group
A security group (`server-sg`) is created for SSH access (port 22) and Jenkins (port 8080) to allow connections from any source (`0.0.0.0/0`).

```hcl
resource "aws_security_group" "server-sg" {
  name        = "server-sg"
  description = "security group for ssh and jenkins"
  vpc_id      = aws_vpc.server-vpc.id

  ingress {
    description = "ssh access"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    description = "Jenkins port"
    from_port   = 8080
    to_port     = 8080
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port        = 0
    to_port          = 0
    protocol         = "-1"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = ["::/0"]
  }
}
```
## EC2 Instances
EC2 instances are created using a for_each loop, deploying jenkins, worker, and ansible instances with the AMI ID ami-04b4f1a9cf54c11d0 and instance type t2.micro.

```hcl
resource "aws_instance" "server" {
    for_each = toset(["jenkins", "worker", "ansible"])
    ami = "ami-04b4f1a9cf54c11d0"
    instance_type = "t2.micro"
    key_name = "server-key"

    vpc_security_group_ids = [aws_security_group.server-sg.id]
    subnet_id = aws_subnet.server-public-subnet-01.id

    depends_on = [ aws_security_group.server-sg, aws_subnet.server-public-subnet-01 ]

    tags = {
        Name = "${each.key}"
    }
}
```
