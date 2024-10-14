---
title : "Create VPC"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.1.1 </b> "
---


#### Create VPC **Lab VPC**
Create a **vpc.tf** with configuration below:

```
resource "aws_vpc" "three-tier-vpc" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "3-tier-vpc"
  }
}

resource "aws_internet_gateway" "three-tier-igw" {
  vpc_id = aws_vpc.three-tier-vpc.id

  tags = {
    Name = "3-tier-igw"
  }
}

resource "aws_eip" "three-tier-eip" {

}

resource "aws_nat_gateway" "three-tier-ngw-01" {
  allocation_id = aws_eip.three-tier-eip.id
  subnet_id     = aws_subnet.public-subnet-01.id

  tags = {
    Name = "three-tier-ngw-01"
  }
  depends_on = [aws_internet_gateway.three-tier-igw]
}
```
