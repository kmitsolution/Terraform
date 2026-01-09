# 📦 Terraform **Local Modules**

## 🌟 What is a Local Module?

> A **local module** is a reusable Terraform module that lives **inside your project directory** and is referenced using a **local file path**.

👉 It helps you **reuse code without publishing it** to Terraform Registry or Git.

---

## 🧠 Why Use Local Modules?

Without local modules:

* Duplicate code
* Hard to manage changes
* Error-prone
* Poor structure

With local modules:

* ✅ Code reuse
* ✅ Cleaner root module
* ✅ Easy refactoring
* ✅ Step toward production-grade Terraform

---

## 🏗️ Typical Project Structure (Local Module)

```
terraform-project/
├── main.tf              # root module
├── variables.tf
├── outputs.tf
└── modules/
    ├── vpc/
    │   ├── main.tf
    │   ├── variables.tf
    │   └── outputs.tf
    └── ec2/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

📌 `modules/` is a **convention**, not a requirement.

---

## 🧱 Example 1: Create a Local **EC2 Module**

### 📁 `modules/ec2/main.tf`

```hcl
resource "aws_instance" "this" {
  ami           = var.ami
  instance_type = var.instance_type
  key_name      = var.key_name

  tags = {
    Name = var.name
  }
}
```

---

### 📁 `modules/ec2/variables.tf`

```hcl
variable "ami" {}
variable "instance_type" {}
variable "key_name" {}
variable "name" {}
```

---

### 📁 `modules/ec2/outputs.tf`

```hcl
output "instance_id" {
  value = aws_instance.this.id
}

output "public_ip" {
  value = aws_instance.this.public_ip
}
```

---

## 📞 Calling the Local Module (Root Module)

### 📁 `main.tf`

```hcl
provider "aws" {
  region = "us-east-1"
}

module "web_ec2" {
  source = "./modules/ec2"

  ami           = "ami-068c0051b15cdb816"
  instance_type = "t2.micro"
  key_name      = "ascendion-awskey"
  name          = "WebServer"
}
```

---

## 📤 Accessing Module Outputs

```hcl
output "web_public_ip" {
  value = module.web_ec2.public_ip
}
```

---

## 🔁 How Data Flows in Local Modules

```
Root Module
   |
   |-- Input variables  ---> Local Module
   |
   |-- Outputs <--- Local Module
```

---

## 🧠 Important Local Module Concepts (Interview MUST-KNOW)

### 🔹 `source`

For local modules:

```hcl
source = "./modules/ec2"
```

Relative paths:

* `./` → current directory
* `../` → parent directory

---

### 🔹 Providers in Local Modules (Best Practice)

❌ Don’t define providers inside child modules
✅ Define provider in root module
✅ Child modules inherit it

---

### 🔹 Multiple Environments with Same Module

```hcl
module "dev_ec2" {
  source = "./modules/ec2"
  name   = "dev-server"
}

module "prod_ec2" {
  source = "./modules/ec2"
  name   = "prod-server"
}
```

---

## ❌ Common Mistakes with Local Modules

❌ Hardcoding values inside module
❌ No variables or outputs
❌ Putting backend config in module
❌ Overloading one module with too many resources

---

## 🧠 Local Modules vs Registry Modules

| Feature     | Local Module                | Registry Module    |
| ----------- | --------------------------- | ------------------ |
| Location    | Local filesystem            | Terraform Registry |
| Reusability | Project-level               | Global             |
| Versioning  | Manual                      | Built-in           |
| Best for    | Learning, internal projects | Production reuse   |

---

## 🎯 Interview-Ready Summary

> **Local modules in Terraform are reusable components stored within the same project directory. They help organize code, reduce duplication, and improve maintainability. Local modules are referenced using relative paths and are commonly used as a foundation before moving to registry-based modules.**

---

## 🧠 One-Line Memory Rule

> **Local module = reusable Terraform code inside your project**

