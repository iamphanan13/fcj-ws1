---
title : "Introduction"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---
**What is 3-tier architecture in simple terms** 


The presentation tier is the user interface, this is where results are translated into something the user can understand. The application tier is where data is processed. The application tier is also called the logic tier because it performs calculations and makes logical decisions. The data tier is where the information that was being processed in the application tier is being stored and data can be retrieved from this tier, passed back to the application tier and eventually back to the user. Three-tier architecture can provide improved scalability, reliability, and security.

In this lab, we will going to create:

- VPC
- 6 subnets: 2 public subnets and 4 private subnets
- 4 EC2 Instances with Amazon Linux 2
- 2 RDS Instances with MySQL 
- Secuirty groups
