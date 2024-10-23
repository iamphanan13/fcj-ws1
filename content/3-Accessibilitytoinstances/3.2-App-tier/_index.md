---
title : "For App tier"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.2 </b> "
---


The Application Tier is where the core functionality of our application app resides, handling tasks such as processing requests and data storage. To ensure the reliability and scalability of our application, we will implement a similar architecture as the Web Tier, but with some additional components.

![App Tier](/images/3.configure/004-apptier.png)


open **instance.tf** and insert configuration below:

```
# ASG for app tier
resource "aws_autoscaling_group" "three-tier-app-asg" {
  name                 = "three-tier-app-asg"
  launch_configuration = aws_launch_configuration.three-tier-app-lconfig.id
  vpc_zone_identifier  = [aws_subnet.private-subnet-01.id, aws_subnet.private-subnet-02.id]
  min_size             = 2
  max_size             = 5
  desired_capacity     = 2
}

# Launch configuration for the app tier
resource "aws_launch_configuration" "three-tier-app-lconfig" {
  name_prefix                 = "three-tier-app-lconfig"
  image_id                    = "ami-018b3a33b9795691a"
  instance_type               = "t2.micro"
  key_name                    = "3-tier-lab"
  security_groups             = [aws_security_group.three-tier-ec2-asg-sg-app.id]
  user_data                   = <<-EOF
                                #!/bin/bash

                                sudo yum install mysql -y

                                EOF
  associate_public_ip_address = false
  lifecycle {
    # prevent_destroy = true
    # ignore_changes  = all
  }
}
```

open **loadbalancer.tf** and insert configuration below


```
# Load balancer - app tier
resource "aws_lb" "three-tier-app-lb" {
  name               = "three-tier-app-lb"
  internal           = true
  load_balancer_type = "application"

  security_groups = [aws_security_group.three-tier-alb-sg-2.id]
  subnets         = [aws_subnet.private-subnet-01.id, aws_subnet.private-subnet-02.id]

  tags = {
    environment = "three-tier-app-lb"
  }
}
# create load balancer target group - app tier
resource "aws_lb_target_group" "three-tier-app-lb-tg" {
  name     = "three-tier-app-lb-tg"
  port     = 80
  protocol = "HTTP"
  vpc_id   = aws_vpc.three-tier-vpc.id

  health_check {
    interval            = 30
    path                = "/"
    port                = "traffic-port"
    protocol            = "HTTP"
    timeout             = 10
    healthy_threshold   = 3
    unhealthy_threshold = 3
  }
}
# Create load balancer listener - app tier
resource "aws_lb_listener" "three-tier-app-lb-listner" {
  load_balancer_arn = aws_lb.three-tier-app-lb.arn
  port              = "80"
  protocol          = "HTTP"
  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.three-tier-app-lb-tg.arn
  }
}

resource "aws_autoscaling_attachment" "three-tier-app-attach" {
  autoscaling_group_name = aws_autoscaling_group.three-tier-app-asg.name
  lb_target_group_arn    = aws_lb_target_group.three-tier-app-lb-tg.arn
}

```
