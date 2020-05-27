---
layout: post
title: Internal ELB creation for autoscaling groups using terraform
categories:
  - cloud
  - examples
excerpt_separator:  <!--more-->
---

<img src="http://roshpr.net/blog/wp-content/uploads/2016/10/terraform_elb.png" alt="Internal ELB creation for autoscaling groups using terraform">

![placeholder](http://roshpr.net/blog/wp-content/uploads/2016/10/terraform_elb.png "Large example image")

### Terraform example with ELB

Terraform script for creation of internal ELB is a bit tricky compared to usual ELB. Internal 
ELB is used between private networks within a ELB. These are used mainly for load balancing 
services within a private VPC network.

The most important attribute to be added for an Internal ELB is "internal = true" . The subnets 
to be added should be internal private subnets. Make sure to add security groups with limited 
access port permissions.

### Internal ELB

```scss
resource "aws_elb" "ielb" {
  name = "ielb"
  security_groups = ["add security group id"]
  subnets = ["add internal private subnets"]
  internal = true

  listener {
    instance_port = 9200
    instance_protocol = "http"
    lb_port = 9200
    lb_protocol = "http"
  }

  health_check {
    healthy_threshold = 2
    unhealthy_threshold = 2
    timeout = 3
    target = "HTTP:9200/"
    interval = 30
  }

  cross_zone_load_balancing = true
  idle_timeout = 400
  connection_draining = true
  connection_draining_timeout = 400
}
```

### Auto scaling group

```
resource "aws_autoscaling_group" "autoscaling" {
  name = "autoscale"
  availability_zones = ["Availability zones"]
  vpc_zone_identifier = ["Private subnets"]
  max_size = 5"
  min_size = "3"
  desired_capacity = "4"
  default_cooldown = 30
  force_delete = true
  launch_configuration = "launch configuration id"
  load_balancers = ["ielb"]
}
```
