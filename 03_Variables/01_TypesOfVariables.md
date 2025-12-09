
✔️ What are **input variables**
✔️ What are **output variables**
✔️ How to **print / reference variables**
✔️ Variable **types** → string, number, list, map, object
✔️ Examples for each type

---

# 🌱 **1. What Are Input Variables?**

**Input variables** allow you to pass values **into** your Terraform configuration.
Think of them like **function parameters**.

They help you:

* reuse code
* customize environments
* avoid hardcoding values

You define them in `variables.tf` and reference them in your Terraform code (`main.tf`).

### Example:

`variables.tf`

```hcl
variable "instance_type" {
  type    = string
  default = "t2.micro"
}
```

Use it inside `main.tf`:

```hcl
instance_type = var.instance_type
```

---

# 🌱 **2. What Are Output Variables?**

**Output variables** show useful information *after* deployment.

Common outputs:

* EC2 public IP
* VPC ID
* Subnet IDs
* Storage account URL

Defined in `outputs.tf`.

### Example:

```hcl
output "server_ip" {
  value = aws_instance.web.public_ip
}
```

After `terraform apply`, Terraform prints:

```
server_ip = "54.21.101.90"
```

---

# 📢 **3. How to Print / Use Input Variables**

### In Terraform code:

```hcl
var.instance_type
```

### In outputs:

```hcl
value = var.instance_type
```

### In CLI (override values):

```bash
terraform apply -var="instance_type=t3.micro"
```

### Using `.tfvars` file:

`dev.tfvars`

```hcl
instance_type = "t3.small"
```

Apply:

```bash
terraform apply -var-file=dev.tfvars
```

---

# 🧩 **4. Terraform Variable Types with Examples**

Let's go through each type:

---

# 1️⃣ **STRING Variable**

Used for text values.

### Define:

```hcl
variable "instance_type" {
  type    = string
  default = "t2.micro"
}
```

### Use:

```hcl
instance_type = var.instance_type
```

---

# 2️⃣ **NUMBER Variable**

Used for numeric values.

### Define:

```hcl
variable "server_count" {
  type    = number
  default = 3
}
```

### Use:

```hcl
count = var.server_count
```

---

# 3️⃣ **LIST Variable**

(Ordered collection of values)

### Example: List of availability zones

### Define:

```hcl
variable "az_list" {
  type = list(string)
  default = ["us-east-1a", "us-east-1b"]
}
```

### Use:

```hcl
availability_zone = var.az_list[0]
```



---

# 4️⃣ **MAP Variable**

(Key-value pairs)

### Example: Map for instance types per environment

### Define:

```hcl
variable "instance_type_map" {
  type = map(string)
  default = {
    dev  = "t2.micro"
    prod = "t3.medium"
  }
}
```

### Use:

```hcl
instance_type = var.instance_type_map["dev"]
```

Or dynamically:

```hcl
instance_type = var.instance_type_map[var.environment]
```

---

# 5️⃣ **OBJECT Variable**

(Structured variable with named fields)

### Example: EC2 configuration object

### Define:

```hcl
variable "server_config" {
  type = object({
    instance_type = string
    disk_size     = number
    tags          = map(string)
  })

  default = {
    instance_type = "t3.micro"
    disk_size     = 20
    tags = {
      Name = "MyServer"
      Env  = "Dev"
    }
  }
}
```

### Use:

```hcl
instance_type = var.server_config.instance_type
tags          = var.server_config.tags
```

---

# 🧩 Full Practical Example

### `variables.tf`

```hcl
variable "server_name" {
  type    = string
  default = "my-ec2"
}

variable "server_count" {
  type    = number
  default = 2
}

variable "tags_list" {
  type    = list(string)
  default = ["web", "backend"]
}

variable "env_map" {
  type = map(string)
  default = {
    dev  = "development"
    prod = "production"
  }
}

variable "server_object" {
  type = object({
    instance_type = string
    volume_size   = number
  })

  default = {
    instance_type = "t2.medium"
    volume_size   = 30
  }
}
```

### `main.tf` (using variables)

```hcl
resource "aws_instance" "example" {
  count         = var.server_count
  ami           = "ami-123456"
  instance_type = var.server_object.instance_type
  tags = {
    Name = var.server_name
    Env  = var.env_map["dev"]
  }
}
```

### `outputs.tf`

```hcl
output "server_name_from_var" {
  value = var.server_name
}
```

---

# 🎯 Summary Table

| Variable Type | Description     | Example                       |
| ------------- | --------------- | ----------------------------- |
| **string**    | Text value      | `"t2.micro"`                  |
| **number**    | Numeric         | `3`                           |
| **list**      | Ordered array   | `["a", "b"]`                  |
| **map**       | Key-value pairs | `{ env = "prod" }`            |
| **object**    | Structured data | `{ name = "app", size = 20 }` |

---

