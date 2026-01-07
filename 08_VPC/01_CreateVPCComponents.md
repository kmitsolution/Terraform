Below is a **FULL, END-TO-END, REAL-WORLD Terraform example** that does exactly what you asked:

 1. Create **VPC**
 2. Create **Public & Private Subnets**
 3. Create **Internet Gateway**
 4. Create **Elastic IP**
 5. Create **NAT Gateway**
 6. Create **Route Tables & Associations**
 7. Launch **EC2 instance inside the VPC**
 8 Extract **Public IP & Private IP** of EC2

This is **production-style** and **copy-paste usable**.

---
Below is a **clear architecture diagram (text-based) + explanation** for the **VPC you created using Terraform**.


---

## 🏗️ **AWS VPC Architecture Diagram (Conceptual)**

```
                         Internet
                             |
                             |
                     +----------------+
                     | InternetGateway|
                     +----------------+
                             |
        -------------------------------------------------
        |                                               |
   Public Route Table                              Private Route Table
   0.0.0.0/0 → IGW                              0.0.0.0/0 → NAT GW
        |                                               |
+------------------+                         +------------------+
|  Public Subnet   |                         | Private Subnet  |
|  10.0.1.0/24     |                         | 10.0.2.0/24     |
|                  |                         |                  |
|  EC2 Instance    |                         |  (No Public IP) |
|  Public IP ✓     |                         |                  |
|  Private IP ✓    |                         |                  |
+------------------+                         +------------------+
        |
        |
+------------------+
|  NAT Gateway     |
|  (Elastic IP)   |
+------------------+
```

---

## 🔍 **Step-by-Step Architecture Explanation**

### 1️⃣ **VPC (Virtual Private Cloud)**

* CIDR: `10.0.0.0/16`
* Provides an isolated network in AWS
* All resources are created **inside this VPC**

👉 Think of it as your **own data center in AWS**

---

### 2️⃣ **Public Subnet**

* CIDR: `10.0.1.0/24`
* Connected to the **Internet Gateway**
* Instances can:

  * Have **public IP**
  * Be accessed from the internet

✔ Used for:

* Web servers
* Bastion hosts
* Load balancers

---

### 3️⃣ **Internet Gateway (IGW)**

* Attached to the VPC
* Enables **internet connectivity**
* Required for:

  * Public IP traffic
  * Inbound/outbound internet access

Rule:

> **Public subnet = Route to IGW**

---

### 4️⃣ **Public Route Table**

Route:

```
0.0.0.0/0 → Internet Gateway
```

Meaning:

* Any traffic going outside VPC goes to IGW

Associated with:

* Public subnet

---

### 5️⃣ **Elastic IP**

* Static public IP provided by AWS
* Attached to **NAT Gateway**
* Allows private subnet resources to access internet

---

### 6️⃣ **NAT Gateway**

* Lives in **public subnet**
* Uses **Elastic IP**
* Allows:

  * Outbound internet access from private subnet
  * ❌ No inbound access

Used for:

* Software updates
* Package downloads
* Security patches

---

### 7️⃣ **Private Subnet**

* CIDR: `10.0.2.0/24`
* No direct internet access
* No public IPs

✔ Used for:

* Databases
* Application servers
* Backend services

---

### 8️⃣ **Private Route Table**

Route:

```
0.0.0.0/0 → NAT Gateway
```

Meaning:

* Private subnet instances can reach internet
* Internet **cannot reach them directly**

---

### 9️⃣ **EC2 Instance**

* Launched in **Public Subnet**
* Has:

  * Public IP → internet access
  * Private IP → internal VPC communication
* Security Group controls:

  * SSH (22)
  * HTTP (80)

---

## 🔐 **Security Design Explanation (Interview Important)**

| Layer                  | Purpose                    |
| ---------------------- | -------------------------- |
| Security Group         | Instance-level firewall    |
| Public subnet          | Internet-facing resources  |
| Private subnet         | Secure backend             |
| NAT Gateway            | Controlled outbound access |
| No public IP (private) | Attack surface reduced     |

---

## 🧠 **Traffic Flow Explained**

### 🌐 Internet → EC2

```
Internet → IGW → Public Route Table → Public Subnet → EC2
```

### 🌐 Private Subnet → Internet

```
Private EC2 → Private Route Table → NAT Gateway → IGW → Internet
```

### ❌ Internet → Private Subnet

```
BLOCKED (No IGW route, no public IP)
```

---

## 🎯 **Interview-Ready Summary (Say This)**

> *This architecture uses a VPC with public and private subnets. The public subnet is connected to the internet via an Internet Gateway and hosts internet-facing resources like EC2. The private subnet has no direct internet access and uses a NAT Gateway with an Elastic IP for outbound connectivity. Route tables control traffic flow, ensuring secure and controlled access.*

---

## 🧠 **One-Line Memory Rule**

> **Public subnet → IGW
> Private subnet → NAT Gateway
> Elastic IP → NAT
> Route tables → traffic control**



# 📁 Recommended Folder Structure

```
vpc-terraform/
├── main.tf
├── variables.tf
├── outputs.tf
```

---

# 1️⃣ Provider & Variables

## **variables.tf**

```hcl
variable "region" {
  default = "us-east-1"
}

variable "vpc_cidr" {
  default = "10.0.0.0/16"
}

variable "public_subnet_cidr" {
  default = "10.0.1.0/24"
}

variable "private_subnet_cidr" {
  default = "10.0.2.0/24"
}

variable "key_name" {
  default = "ascendion-awskey"
}
```

---

## **main.tf**

```hcl
provider "aws" {
  region = var.region
}
```

---

# 2️⃣ Create VPC

```hcl
resource "aws_vpc" "main_vpc" {
  cidr_block           = var.vpc_cidr
  enable_dns_support   = true
  enable_dns_hostnames = true

  tags = {
    Name = "Terraform-VPC"
  }
}
```

---

# 3️⃣ Create Internet Gateway

```hcl
resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.main_vpc.id

  tags = {
    Name = "Terraform-IGW"
  }
}
```

---

# 4️⃣ Create Public Subnet

```hcl
resource "aws_subnet" "public_subnet" {
  vpc_id                  = aws_vpc.main_vpc.id
  cidr_block              = var.public_subnet_cidr
  map_public_ip_on_launch = true
  availability_zone       = "us-east-1a"

  tags = {
    Name = "Public-Subnet"
  }
}
```

---

# 5️⃣ Create Private Subnet

```hcl
resource "aws_subnet" "private_subnet" {
  vpc_id            = aws_vpc.main_vpc.id
  cidr_block        = var.private_subnet_cidr
  availability_zone = "us-east-1a"

  tags = {
    Name = "Private-Subnet"
  }
}
```

---

# 6️⃣ Create Elastic IP (For NAT Gateway)

```hcl
resource "aws_eip" "nat_eip" {
  domain = "vpc"
}
```

---

# 7️⃣ Create NAT Gateway (Inside Public Subnet)

```hcl
resource "aws_nat_gateway" "nat_gw" {
  allocation_id = aws_eip.nat_eip.id
  subnet_id     = aws_subnet.public_subnet.id

  tags = {
    Name = "Terraform-NAT"
  }

  depends_on = [aws_internet_gateway.igw]
}
```

---

# 8️⃣ Public Route Table

```hcl
resource "aws_route_table" "public_rt" {
  vpc_id = aws_vpc.main_vpc.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }

  tags = {
    Name = "Public-RT"
  }
}
```

---

# 9️⃣ Associate Public Route Table

```hcl
resource "aws_route_table_association" "public_assoc" {
  subnet_id      = aws_subnet.public_subnet.id
  route_table_id = aws_route_table.public_rt.id
}
```

---

# 🔟 Private Route Table (Via NAT Gateway)

```hcl
resource "aws_route_table" "private_rt" {
  vpc_id = aws_vpc.main_vpc.id

  route {
    cidr_block     = "0.0.0.0/0"
    nat_gateway_id = aws_nat_gateway.nat_gw.id
  }

  tags = {
    Name = "Private-RT"
  }
}
```

---

# 1️⃣1️⃣ Associate Private Route Table

```hcl
resource "aws_route_table_association" "private_assoc" {
  subnet_id      = aws_subnet.private_subnet.id
  route_table_id = aws_route_table.private_rt.id
}
```

---

# 1️⃣2️⃣ Security Group for EC2

```hcl
resource "aws_security_group" "web_sg" {
  vpc_id = aws_vpc.main_vpc.id

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
    Name = "Web-SG"
  }
}
```

---

# 1️⃣3️⃣ Create EC2 Instance (Public Subnet)

```hcl
resource "aws_instance" "web" {
  ami                    = "ami-068c0051b15cdb816" # Amazon Linux 2
  instance_type          = "t2.micro"
  key_name               = var.key_name
  subnet_id              = aws_subnet.public_subnet.id
  vpc_security_group_ids = [aws_security_group.web_sg.id]

  tags = {
    Name = "Terraform-EC2"
  }
}
```

---

# 1️⃣4️⃣ Extract EC2 Public & Private IP

## **outputs.tf**

```hcl
output "ec2_public_ip" {
  value = aws_instance.web.public_ip
}

output "ec2_private_ip" {
  value = aws_instance.web.private_ip
}
```

---

# 🚀 How to Run

```bash
terraform init
terraform validate
terraform plan
terraform apply
```

---

# 🧠 Architecture Flow (Interview Explanation)

```
VPC
 ├── Public Subnet → IGW → Internet
 │      └── EC2 (Public IP)
 │
 └── Private Subnet → NAT Gateway → Internet
```

---

# 🎯 Interview-Ready Summary

> **This Terraform configuration creates a complete AWS VPC with public and private subnets, an Internet Gateway, NAT Gateway with Elastic IP, appropriate route tables, and an EC2 instance. The EC2 instance is launched in the public subnet, and its public and private IP addresses are exposed using Terraform outputs.**

---

# 🧠 One-Line Memory Rule

> **Public subnet → IGW
> Private subnet → NAT Gateway
> Elastic IP → NAT
> Outputs → IP extraction**

---

