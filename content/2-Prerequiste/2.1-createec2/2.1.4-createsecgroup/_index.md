---
title : "Create security groups"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 2.1.4 </b> "
---

#### Create security groups

In this step, we will proceed to create the security groups for alb used for our architecture. As you can see, we will create 3 different security groups for 3 tiers of application.


Create **security.tf** and insert configuration below

```
# Load balancer security group 1 - web
resource "aws_security_group" "three-tier-alb-sg-1" {
  vpc_id = aws_vpc.three-tier-vpc.id
  depends_on = [
    aws_vpc.three-tier-vpc
  ]

  ingress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "three-tier-alb-sg-1"
  }
}
# Load balancer security group 2 - app 
resource "aws_security_group" "three-tier-alb-sg-2" {
  name        = "three-tier-alb-sg-2"
  description = "load balancer security group for app tier"
  vpc_id      = aws_vpc.three-tier-vpc.id
  depends_on = [
    aws_vpc.three-tier-vpc
  ]

  ingress {
    from_port       = "80"
    to_port         = "80"
    protocol        = "tcp"
    security_groups = [aws_security_group.three-tier-alb-sg-1.id]
  }

  tags = {
    Name = "three-tier-alb-sg-2"
  }
}

###########################################################################################

# web tier auto scalling group - Security Group
resource "aws_security_group" "three-tier-ec2-asg-sg" {
  vpc_id = aws_vpc.three-tier-vpc.id
  depends_on = [
    aws_vpc.three-tier-vpc
  ]


  ingress {
    from_port = "0"
    to_port   = "0"
    protocol  = "-1"
  }
  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "three-tier-ec2-asg-sg"
  }
}

# app tier auto scalling group - Security Group
resource "aws_security_group" "three-tier-ec2-asg-sg-app" {
  name        = "three-tier-ec2-asg-sg-app"
  description = "Allow traffic from web tier"
  vpc_id      = aws_vpc.three-tier-vpc.id
  depends_on = [
    aws_vpc.three-tier-vpc
  ]

  ingress {
    from_port       = "-1"
    to_port         = "-1"
    protocol        = "icmp"
    security_groups = [aws_security_group.three-tier-ec2-asg-sg.id]
  }
  ingress {
    from_port       = "80"
    to_port         = "80"
    protocol        = "tcp"
    security_groups = [aws_security_group.three-tier-ec2-asg-sg.id]
  }
  ingress {
    from_port       = "22"
    to_port         = "22"
    protocol        = "tcp"
    security_groups = [aws_security_group.three-tier-ec2-asg-sg.id]
  }
  egress {
    from_port   = "0"
    to_port     = "0"
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "three-tier-ec2-asg-sg-app"
  }
}


# Database tier security group

resource "aws_security_group" "three-tier-db-sg" {
  name        = "three-tier-db-sg"
  description = "Allow traffic from app tier"
  vpc_id      = aws_vpc.three-tier-vpc.id

  ingress {
    from_port   = 3306
    to_port     = 3306
    protocol    = "tcp"
    cidr_blocks = ["10.0.3.0/24", "10.0.4.0/24"]
    description = "allow ssh from app tier"
  }
  ingress {
    from_port       = 22
    to_port         = 22
    protocol        = "tcp"
    security_groups = [aws_security_group.three-tier-ec2-asg-sg-app.id]
    cidr_blocks     = ["10.0.0.0/16"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

```
Now, let opn AWS Console and check the resources:

### VPC, NAT Gateway, IGW and Route tables