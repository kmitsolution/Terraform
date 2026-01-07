#  Auto Scaling Group in Terraform (Using **Default VPC**)

---

##  Architecture (Default VPC)

```
Default VPC
   |
   |-- Default Subnet (AZ-a)
   |-- Default Subnet (AZ-b)
   |-- Default Subnet (AZ-c)
           |
     Auto Scaling Group
           |
      EC2 Instances
```

---

## 1️⃣ Provider

```hcl
provider "aws" {
  region = "us-east-1"
}
```

---

## 2️⃣ Fetch Default VPC

```hcl
data "aws_vpc" "default" {
  default = true
}
```

---

## 3️⃣ Fetch Default Subnets

```hcl
data "aws_subnets" "default" {
  filter {
    name   = "vpc-id"
    values = [data.aws_vpc.default.id]
  }
}
```

✔ These subnets already:

* Are public
* Have Internet Gateway
* Auto-assign public IPs

---

## 4️⃣ Security Group (in Default VPC)

```hcl
resource "aws_security_group" "asg_sg" {
  name   = "asg-sg"
  vpc_id = data.aws_vpc.default.id

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 80
    to_port     = 80
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
    Name = "ASG-SG"
  }
}
```

---

## 5️⃣ Launch Template

⚠️ **user_data must be base64 encoded**

```hcl
resource "aws_launch_template" "web_lt" {
  name_prefix   = "web-lt-"
  image_id      = "ami-068c0051b15cdb816" # Amazon Linux 2
  instance_type = "t2.micro"
  key_name      = "ascendion-awskey"

  vpc_security_group_ids = [aws_security_group.asg_sg.id]

  user_data = base64encode(<<-EOF
    #!/bin/bash
    yum install -y httpd
    systemctl start httpd
    systemctl enable httpd
    echo "Hello from ASG using Default VPC" > /var/www/html/index.html
  EOF)
}
```

---

## 6️⃣ Auto Scaling Group (DEFAULT VPC)

```hcl
resource "aws_autoscaling_group" "web_asg" {
  min_size         = 1
  desired_capacity = 2
  max_size         = 3

  vpc_zone_identifier = data.aws_subnets.default.ids

  launch_template {
    id      = aws_launch_template.web_lt.id
    version = "$Latest"
  }

  health_check_type         = "EC2"
  health_check_grace_period = 300

  tag {
    key                 = "Name"
    value               = "ASG-DefaultVPC"
    propagate_at_launch = true
  }
}
```

---

## 🔍 What Terraform Creates

✔ Uses **default VPC**
✔ Uses **all default subnets (multi-AZ)**
✔ Creates Launch Template
✔ Creates Auto Scaling Group
✔ Launches **2 EC2 instances**
✔ Installs Apache via `user_data`

---

## 🌐 How to Verify

1. Go to **EC2 → Instances**
2. You’ll see **2 instances** created by ASG
3. Copy **Public IP**
4. Open browser:

```
http://<public-ip>
```

You’ll see:

```
Hello from ASG using Default VPC
```

---

## 🧠 Interview-Important Points

### 🔹 Why use Default VPC?

* Learning / POC
* Faster setup
* No networking complexity

### 🔹 Why data sources?

* Default resources already exist
* Terraform should not recreate them

### 🔹 Why base64encode user_data?

* Required by Launch Template API

---

## 🎯 Interview-Ready Summary

> **This Terraform configuration creates an Auto Scaling Group using the default VPC by fetching the default VPC and its subnets through data sources. A launch template defines EC2 configuration, and the ASG distributes instances across all default subnets for high availability. This approach avoids custom networking while still demonstrating scalable infrastructure.**

---

## 🧠 One-Line Memory Rule

> **Learning → Default VPC
> Production → Custom VPC**

---

