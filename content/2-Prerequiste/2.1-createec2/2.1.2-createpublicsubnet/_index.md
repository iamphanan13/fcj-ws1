---
title : "Create Public and Private Subnet"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.1.2 </b> "
---

#### Create Public and Private Subnet

Create a **subnet.tf** with configuration below:

```
resource "aws_subnet" "public-subnet-01" {
  vpc_id                  = aws_vpc.three-tier-vpc.id
  cidr_block              = "10.0.1.0/24"
  availability_zone       = "ap-southeast-1a"
  map_public_ip_on_launch = true


  tags = {
    Name = "public-subnet-01"
  }
}

resource "aws_subnet" "public-subnet-02" {
  vpc_id                  = aws_vpc.three-tier-vpc.id
  cidr_block              = "10.0.2.0/24"
  availability_zone       = "ap-southeast-1b"
  map_public_ip_on_launch = true


  tags = {
    Name = "public-subnet-02"
  }
}

resource "aws_subnet" "private-subnet-01" {
  vpc_id            = aws_vpc.three-tier-vpc.id
  availability_zone = "ap-southeast-1a"
  cidr_block        = "10.0.3.0/24"

  tags = {
    Name = "private-subnet-01"
  }
}

resource "aws_subnet" "private-subnet-02" {
  vpc_id            = aws_vpc.three-tier-vpc.id
  cidr_block        = "10.0.4.0/24"
  availability_zone = "ap-southeast-1b"

  tags = {
    Name = "private-subnet-02"
  }
}

resource "aws_subnet" "private-subnet-03" {
  vpc_id            = aws_vpc.three-tier-vpc.id
  cidr_block        = "10.0.5.0/24"
  availability_zone = "ap-southeast-1a"

  tags = {
    Name = "private-subnet-03"
  }
}
resource "aws_subnet" "private-subnet-04" {
  vpc_id            = aws_vpc.three-tier-vpc.id
  cidr_block        = "10.0.6.0/24"
  availability_zone = "ap-southeast-1b"

  tags = {
    Name = "private-subnet-04"
  }
}



```

We have **6 subnets** with **2 public subnets** and **4 private subnets**

- Public subnet 1 (CIDR: **10.0.1.0/24**)
- Public subnet 2 (CIDR: **10.0.2.0/24**)
- Private subnet 3 (CIDR: **10.0.3.0/24**)
- Private subnet 4 (CIDR: **10.0.4.0/24**)
- Private subnet 5 (CIDR: **10.0.5.0/24**)
- Private subnet 6 (CIDR: **10.0.6.0/24**)
