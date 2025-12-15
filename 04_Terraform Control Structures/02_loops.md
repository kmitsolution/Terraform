
# 🌱 **Loops in Terraform**

Terraform supports loops in three ways:

1. **`count`** → repeats a resource **N times**
2. **`for_each`** → loops through **lists or maps**
3. **`for` expressions** → transforms lists/maps into new lists/maps

Let’s learn each one with easy examples.

---

# 🟦 **1. `count` — Repeat a resource N times**

### ✔️ Use when:

* You want **multiple identical** resources
* You loop using a **number**
* You don’t need unique keys for each item

### 📘 **Example: Create 3 EC2 instances**

```hcl
resource "aws_instance" "server" {
  count         = 3
  ami           = "ami-123456"
  instance_type = "t2.micro"

  tags = {
    Name = "Server-${count.index}"
  }
}
```

### `count.index` values:

| Instance | Name     |
| -------- | -------- |
| 0        | Server-0 |
| 1        | Server-1 |
| 2        | Server-2 |

---

# 🟩 **2. `for_each` — Loop through lists or maps**

### ✔️ Use when:

* Items must have **unique names**
* You need stable addressing (`for_each` prevents recreation)
* You want control over **resource identity**
* You loop through **lists or maps**

---

## 📘 **Example: Create security groups for each port**

Create Security group without for_each loop

```
provider "aws" {
  region = var.region
}

data "aws_vpc" "default" {
  default = true
}
output "name" {
  value = data.aws_vpc.default.id
}

resource "aws_security_group" "sg1" {
  name        = "allow_ssh_http"
  description = "Allow SSH and HTTP inbound traffic"
  vpc_id      = data.aws_vpc.default.id

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
}
```
Got it 👍
Below I’ll **first show the solution WITHOUT `for_each`**, then **rewrite it USING `for_each`**, and finally **explain why `for_each` is better**.

---

## ✅ **PART 1: WITHOUT `for_each` (Explicit Rules)**

### **variables.tf**

```hcl
variable "ports" {
  type    = list(number)
  default = [22, 80]
}
```

---

### **main.tf**

```hcl
provider "aws" {
  region = "us-east-1"
}

# Fetch default VPC
data "aws_vpc" "default" {
  default = true
}

# Create Security Group
resource "aws_security_group" "web_sg" {
  name        = "allow-ssh-http"
  description = "Allow SSH and HTTP"
  vpc_id      = data.aws_vpc.default.id

  tags = {
    Name = "allow-ssh-http"
  }
}

# SSH Rule (Port 22)
resource "aws_security_group_rule" "ssh" {
  type              = "ingress"
  from_port         = var.ports[0]   # 22
  to_port           = var.ports[0]
  protocol          = "tcp"
  cidr_blocks       = ["0.0.0.0/0"]
  security_group_id = aws_security_group.web_sg.id
}

# HTTP Rule (Port 80)
resource "aws_security_group_rule" "http" {
  type              = "ingress"
  from_port         = var.ports[1]   # 80
  to_port           = var.ports[1]
  protocol          = "tcp"
  cidr_blocks       = ["0.0.0.0/0"]
  security_group_id = aws_security_group.web_sg.id
}
```

---

### 🔍 **What Happens Here**

* One Security Group is created
* Two **separate ingress rules** are defined manually
* Works fine for **small, fixed number of ports**

---

## ❌ **Limitations of This Approach**

* Repetitive code
* Hard to scale (adding port 443 = new resource block)
* Index-based access (`var.ports[0]`) is fragile
* Error-prone if order changes

---

---

## ✅ **PART 2: USING `for_each` (Recommended Way)**

### **variables.tf**

```hcl
variable "ports" {
  type    = list(number)
  default = [22, 80]
}
```

---

### **main.tf**

```hcl
provider "aws" {
  region = "us-east-1"
}

# Fetch default VPC
data "aws_vpc" "default" {
  default = true
}

# Create Security Group
resource "aws_security_group" "web_sg" {
  name        = "allow-ssh-http"
  description = "Allow SSH and HTTP"
  vpc_id      = data.aws_vpc.default.id
}

# Ingress rules using for_each
resource "aws_security_group_rule" "allow_ports" {
  for_each = toset(var.ports)

  type              = "ingress"
  from_port         = each.value
  to_port           = each.value
  protocol          = "tcp"
  cidr_blocks       = ["0.0.0.0/0"]
  security_group_id = aws_security_group.web_sg.id
}
```

---

### 🔍 **What Happens Here**

Terraform automatically creates:

* Rule for port **22**
* Rule for port **80**

No duplicated code.

---

## 🧠 **WHY `for_each` IS BETTER (Interview Answer)**

| Without `for_each`         | With `for_each`            |
| -------------------------- | -------------------------- |
| Manual duplication         | Single clean block         |
| Hard to scale              | Easy to add/remove ports   |
| Index-based (`[0]`, `[1]`) | Value-based (`each.value`) |
| Risk of recreation         | Stable resource identity   |
| Not reusable               | Production-ready           |

---

### ✔️ Adding HTTPS (443) becomes easy:

```hcl
default = [22, 80, 443]
```

No code change required.

---

## 📘 **Example with map (key-value)**

```hcl
variable "instances" {
  default = {
    web  = "t2.micro"
    db   = "t3.small"
  }
}

resource "aws_instance" "servers" {
  for_each = var.instances

  ami           = "ami-123456"
  instance_type = each.value

  tags = {
    Name = each.key
  }
}
```

Creates:

| Name | Instance Type |
| ---- | ------------- |
| web  | t2.micro      |
| db   | t3.small      |

---

# 🟨 **3. `for` Expressions — Transform lists or maps**

These are not loops that create resources.
They are used to **build new lists/maps from existing ones**.

### ✔️ Use when:

* You want to transform a list
* You want to filter items
* You want to compute a value

---

## 📘 **Example: Convert list to uppercase**

```hcl
locals {
  names = ["dev", "stage", "prod"]

  upper_names = [for n in local.names : upper(n)]
}
```

Result:

```
["DEV", "STAGE", "PROD"]
```

---

## 📘 **Example: Filter only even numbers**

```hcl
locals {
  numbers = [1, 2, 3, 4, 5, 6]

  even_numbers = [for n in local.numbers : n if n % 2 == 0]
}
```

Result:

```
[2, 4, 6]
```

---

## 📘 **Example: Convert map values**

```hcl
locals {
  instance_map = {
    web  = 1
    app  = 2
    db   = 3
  }

  size_map = { for k, v in local.instance_map : k => v * 10 }
}
```

Result:

```
{
  web = 10
  app = 20
  db  = 30
}
```
# 🌍 Real-Time Scenario

You are creating an **EC2 instance** and want to apply **standard tags** for multiple environments (dev, stage, prod).

---

## ❌ PART 1: WITHOUT `for` LOOP (Manual & Static)

### `locals.tf`

```hcl
locals {
  env_tags = {
    dev   = "DEV"
    stage = "STAGE"
    prod  = "PROD"
  }
}
```

### `main.tf`

```hcl
resource "aws_instance" "example" {
  ami           = "ami-123456"
  instance_type = "t2.micro"

  tags = {
    DevEnv   = local.env_tags.dev
    StageEnv = local.env_tags.stage
    ProdEnv  = local.env_tags.prod
  }
}
```

### 🔴 Problems

* Manual mapping
* Hard to extend
* Not reusable
* Not dynamic

---

## ✅ PART 2: WITH **`for` LOOP (for expression)**

Now we generate the tags **dynamically** using a **Terraform `for` loop**.

---

### `variables.tf`

```hcl
variable "environments" {
  type    = list(string)
  default = ["dev", "stage", "prod"]
}
```

---

### `locals.tf`

```hcl
locals {
  env_tags = {
    for env in var.environments :
    env => upper(env)
  }
}
```

### What this `for` loop does:

```hcl
for env in var.environments : env => upper(env)
```

It converts:

```
["dev", "stage", "prod"]
```

Into:

```
{
  dev   = "DEV"
  stage = "STAGE"
  prod  = "PROD"
}
```

---

### `main.tf`

```hcl
resource "aws_instance" "example" {
  ami           = "ami-123456"
  instance_type = "t2.micro"

  tags = local.env_tags
}
```

---

## 🧠 Why This Is a **Real `for` Loop**

This is a **Terraform `for` expression**, not `for_each`.

✔ It **creates a new map**
✔ It **transforms data**
✔ It does **NOT create resources**
✔ Used inside **locals, variables, outputs**

---

# 🔍 Another Simple Example (AZs)

### Scenario

You want to **store AZ names in uppercase**.

### Input

```hcl
variable "azs" {
  default = ["us-east-1a", "us-east-1b"]
}
```

### Using `for` loop

```hcl
locals {
  az_upper = [for az in var.azs : upper(az)]
}
```

### Result

```
["US-EAST-1A", "US-EAST-1B"]
```

---

# 🧠 Key Difference (Very Important)

| Feature           | `for` loop (for expression) | `for_each`       |
| ----------------- | --------------------------- | ---------------- |
| Purpose           | Transform data              | Create resources |
| Creates resources | ❌ No                        | ✔ Yes            |
| Used in           | locals, variables, outputs  | resources        |
| Example           | list → map                  | map → resources  |

---


---

# 🎯 **When to Use Which Loop?**

| Feature            | `count`             | `for_each`                    | `for` Expression   |
| ------------------ | ------------------- | ----------------------------- | ------------------ |
| Use case           | repeat N times      | loop through list/map         | transform list/map |
| Best for           | identical resources | uniquely identified resources | generating values  |
| Index?             | `count.index`       | `each.key`, `each.value`      | each item          |
| Identity stable?   | ❌ No                | ✔️ Yes                        | ✔️ Yes             |
| Creates resources? | ✔️ Yes              | ✔️ Yes                        | ❌ No               |

---

# 🧠 **Rule of Thumb**

### ✔️ Use **count**

When resources are identical & ordered (0,1,2,…)

### ✔️ Use **for_each**

When resources must be uniquely addressed using keys (like names or maps)

### ✔️ Use **for** expressions

When transforming data into a new list or map

---

# 🏁 Summary Examples (Quick)

### ✔️ count

```hcl
count = 3
```

### ✔️ for_each (list)

```hcl
for_each = toset(var.ports)
```

### ✔️ for_each (map)

```hcl
for_each = var.instances
```

### ✔️ for expression

```hcl
[for x in var.list : upper(x)]
```

---

