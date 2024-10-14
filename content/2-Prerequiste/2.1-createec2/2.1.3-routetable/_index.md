---
title : "Create Route Table and Subnet Association"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 2.1.3 </b> "
---

#### Create Route Table and Subnet Association

Create a **routetable.tf** with configuration below:
```
resource "aws_route_table" "three-tier-web-rt" {
  vpc_id = aws_vpc.three-tier-vpc.id
  tags = {
    Name = "three-tier-web-rt"
  }

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.three-tier-igw.id
  }
}

resource "aws_route_table" "three-tier-app-rt" {
  vpc_id = aws_vpc.three-tier-vpc.id
  tags = {
    Name = "three-tier-app-rt"
  }
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_nat_gateway.three-tier-ngw-01.id
  }
}

resource "aws_route_table_association" "three-tier-rta-01" {
  subnet_id      = aws_subnet.public-subnet-01.id
  route_table_id = aws_route_table.three-tier-web-rt.id
}

resource "aws_route_table_association" "three-tier-rta-02" {
  subnet_id      = aws_subnet.public-subnet-02.id
  route_table_id = aws_route_table.three-tier-web-rt.id
}

resource "aws_route_table_association" "three-tier-rta-03" {
  subnet_id      = aws_subnet.private-subnet-01.id
  route_table_id = aws_route_table.three-tier-app-rt.id
}

resource "aws_route_table_association" "three-tier-rta-04" {
  subnet_id      = aws_subnet.private-subnet-02.id
  route_table_id = aws_route_table.three-tier-app-rt.id
}

resource "aws_route_table_association" "three-tier-rta-05" {
  subnet_id      = aws_subnet.private-subnet-03.id
  route_table_id = aws_route_table.three-tier-app-rt.id
}

resource "aws_route_table_association" "three-tier-rta-06" {
  subnet_id      = aws_subnet.private-subnet-04.id
  route_table_id = aws_route_table.three-tier-app-rt.id

}
```