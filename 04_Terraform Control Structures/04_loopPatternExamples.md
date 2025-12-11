

# 🌱 **1. Loop Pattern for TAGS (Common & Simple)**

## 🎯 Goal:

Apply multiple key/value tags using loops.

### Input variables:

```hcl
variable "tags" {
  type = map(string)
  default = {
    Project = "TerraformDemo"
    Owner   = "DevOpsTeam"
    Env     = "Dev"
  }
}
```

### Using `for_each` to apply tags:

```hcl
resource "aws_instance" "example" {
  ami           = "ami-123456"
  instance_type = "t2.micro"

  tags = { for key, value in var.tags : key => value }
}
```

### Resulting tags:

```
Project = TerraformDemo
Owner   = DevOpsTeam
Env     = Dev
```

---

# 🌱 **2. Loop Pattern for SECURITY GROUP RULES**

This is the **most common use case**:
You pass a list of ports → Terraform creates SG rules automatically.

## 🎯 Goal:

Allow ports 22, 80, and 443 without writing 3 SG blocks.

### Input:

```hcl
variable "ports" {
  type    = list(number)
  default = [22, 80, 443]
}
```

### Security group rule using `for_each`:

```hcl
resource "aws_security_group_rule" "allow_ports" {
  for_each = toset(var.ports)

  type              = "ingress"
  from_port         = each.value
  to_port           = each.value
  protocol          = "tcp"
  cidr_blocks       = ["0.0.0.0/0"]
  security_group_id = aws_security_group.my_sg.id
}
```

### Explanation:

* Terraform creates **one rule per port**
* `each.value` becomes → 22, 80, 443

---

# 🌱 **3. Loop Pattern for MULTIPLE SUBNETS**

## 🌍 Example: Create 3 subnets from a list of CIDRs

### Input:

```hcl
variable "subnet_cidrs" {
  type = list(string)
  default = ["10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24"]
}
```

### Resource:

```hcl
resource "aws_subnet" "subnets" {
  for_each = toset(var.subnet_cidrs)

  vpc_id     = aws_vpc.main.id
  cidr_block = each.value

  tags = {
    Name = "Subnet-${each.value}"
  }
}
```

This creates:

* Subnet-10.0.1.0/24
* Subnet-10.0.2.0/24
* Subnet-10.0.3.0/24

---

# 🌱 **4. Subnets Using Map (for naming)**

### Input:

```hcl
variable "subnets" {
  type = map(string)
  default = {
    public1  = "10.0.1.0/24"
    public2  = "10.0.2.0/24"
    private1 = "10.0.10.0/24"
  }
}
```

### Resource:

```hcl
resource "aws_subnet" "vpc_subnets" {
  for_each = var.subnets

  vpc_id     = aws_vpc.main.id
  cidr_block = each.value

  tags = {
    Name = each.key
  }
}
```

Creates subnets with names:

* public1
* public2
* private1

---

# 🌱 **5. Loop Pattern for MULTIPLE EC2 INSTANCES**

Create multiple EC2s with **different** instance types:

### Input:

```hcl
variable "servers" {
  default = {
    web = "t2.micro"
    app = "t3.micro"
    db  = "t3.medium"
  }
}
```

### Resource:

```hcl
resource "aws_instance" "servers" {
  for_each = var.servers

  ami           = "ami-123456"
  instance_type = each.value

  tags = {
    Name = each.key
  }
}
```

Terraform creates:

* web → t2.micro
* app → t3.micro
* db → t3.medium

---

# 🌱 **6. Loop Pattern for ATTACHING ROUTE TABLES TO SUBNETS**

### Input:

```hcl
variable "public_subnets" {
  default = ["subnet-001", "subnet-002"]
}
```

### Resource:

```hcl
resource "aws_route_table_association" "assoc" {
  for_each = toset(var.public_subnets)

  subnet_id      = each.value
  route_table_id = aws_route_table.public.id
}
```

---

# 🌱 **7. Loop Pattern for MULTIPLE IAM USERS**

### Input:

```hcl
variable "users" {
  default = ["alice", "bob", "charlie"]
}
```

### Resource:

```hcl
resource "aws_iam_user" "team" {
  for_each = toset(var.users)

  name = each.value
}
```

---

# 🌱 **8. Loop Pattern for MULTIPLE TAGS Using for Expressions**

### Input:

```hcl
variable "tag_envs" {
  default = ["dev", "stage", "prod"]
}
```

### Create tag map using for expression:

```hcl
locals {
  env_tags = { for env in var.tag_envs : env => upper(env) }
}
```

### Result:

```
{
  dev   = "DEV"
  stage = "STAGE"
  prod  = "PROD"
}
```

---

# 🎯 **Summary — Common Loop Patterns**

| Pattern      | Loop Type           | Use Case                  |
| ------------ | ------------------- | ------------------------- |
| Tags         | for expression      | Convert/loop through maps |
| SG Rules     | for_each + list     | Create rule per port      |
| Subnets      | for_each + list/map | Create multiple subnets   |
| EC2          | for_each            | Different properties      |
| Associations | for_each            | Distributed linking       |

---

