---
title : "For Web tier"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.1  </b> "
---

The Web Tier, also known as the ‘Presentation’ tier, is the environment where our application will be delivered for users to interact with. Here's an architecture:

![Web Tier](/images/3.configure/003-webtier.png)


Create new file called **instance.tf** and insert configuration below:

```
# ASG for web tier
resource "aws_autoscaling_group" "three-tier-web-asg" {
  name                 = "three-tier-web-asg"
  launch_configuration = aws_launch_configuration.three-tier-web-lconfig.id
  vpc_zone_identifier  = [aws_subnet.public-subnet-01.id, aws_subnet.public-subnet-02.id]
  min_size             = 2
  max_size             = 5
  desired_capacity     = 2
}

# Launch configuration for the web tier
resource "aws_launch_configuration" "three-tier-web-lconfig" {
  name_prefix     = "three-tier-web-lconfig"
  image_id        = "ami-018b3a33b9795691a"
  instance_type   = "t2.micro"
  key_name        = "3-tier-lab"
  security_groups = [aws_security_group.three-tier-ec2-asg-sg.id]
  user_data       = <<-EOF
  #!/bin/bash
yum install httpd -y
service httpd start
chconfig httpd on

cd /var/www/html
echo "<html>" > index.html

echo "<h1>Welcome to 3 tier</h1>" >> index.html
echo "<h4>You are running instance from this IP (For debug only!!!!Do not public this to user):</h4>" >> index.html

export TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"`

echo "<br>Private IP: " >> index.html
curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/local-ipv4 >> index.html

echo "<br>Public IP: " >> index.html
curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/public-ipv4 >> index.html 
echo "</html>" >> index.html

EOF

  associate_public_ip_address = true
  lifecycle {
    # prevent_destroy = true
    # ignore_changes  = all
  }

}
```

and create new file called **loadbalancer.tf** and insert configuration below


```
# Load balancer - web tier
resource "aws_lb" "three-tier-web-lb" {
  name               = "three-tier-web-lb"
  internal           = false
  load_balancer_type = "application"

  security_groups = [aws_security_group.three-tier-alb-sg-1.id]
  subnets         = [aws_subnet.public-subnet-01.id, aws_subnet.public-subnet-02.id]

  tags = {
    environment = "three-tier-web-lb"
  }
}

# create load balancer target group - web tier
resource "aws_lb_target_group" "three-tier-web-lb-tg" {
  name     = "three-tier-web-lb-tg"
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

# Create load balancer listener - web tier
resource "aws_lb_listener" "three-tier-web-lb-listener" {
  load_balancer_arn = aws_lb.three-tier-web-lb.arn
  port              = 80
  protocol          = "HTTP"
  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.three-tier-web-lb-tg.arn
  }
}

resource "aws_autoscaling_attachment" "three-tier-web-attach" {
  autoscaling_group_name = aws_autoscaling_group.three-tier-web-asg.name
  lb_target_group_arn    = aws_lb_target_group.three-tier-web-lb-tg.arn
}
```