
# 📦 Terraform Modules – Introduction

## 🌟 What is a Terraform Module?

> **A Terraform module is a reusable collection of Terraform configuration files that encapsulates a specific piece of infrastructure.**

In simple terms:

* A module is a **folder with `.tf` files**
* It performs **one logical task**
* It can be reused across environments and projects

---

## 🧠 Why Do We Need Modules?

Without modules:

* Repetitive code
* Hard to maintain
* Error-prone
* Poor scalability

With modules:

* **Reusability**
* **Consistency**
* **Cleaner code**
* **Standardization**

---

## 🏗️ Types of Terraform Modules

### 1️⃣ Root Module

* The **main working directory**
* Where you run `terraform init/plan/apply`

Example:

```
main.tf
variables.tf
outputs.tf
```

👉 Every Terraform project has **one root module**

---

### 2️⃣ Child Modules

* Called by the root module
* Encapsulate reusable logic

Example:

* VPC module
* EC2 module
* IAM module

---

### 3️⃣ Registry Modules

* Public modules from **Terraform Registry**
* Example: `terraform-aws-modules/vpc/aws`

---

## 📁 Basic Module Folder Structure

```
terraform-project/
├── main.tf          (root module)
├── variables.tf
├── outputs.tf
└── modules/
    └── ec2/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

---

## 🔧 Simple Example: EC2 Module

### 📁 modules/ec2/main.tf

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

### 📁 modules/ec2/variables.tf

```hcl
variable "ami" {}
variable "instance_type" {}
variable "key_name" {}
variable "name" {}
```

---

### 📁 modules/ec2/outputs.tf

```hcl
output "instance_id" {
  value = aws_instance.this.id
}
```

---

## 📞 Calling the Module from Root Module

### 📁 main.tf

```hcl
module "web_ec2" {
  source = "./modules/ec2"

  ami           = "ami-068c0051b15cdb816"
  instance_type = "t2.micro"
  key_name      = "ascendion-awskey"
  name          = "WebServer"
}
```

---

## 🔁 How Data Flows in Modules

```
Root Module
   |
   |-- variables → Child Module
   |
   |-- outputs   ← Child Module
```

* Inputs → variables
* Outputs → exposed values

---

## 🧠 Important Module Concepts (Interview MUST-KNOW)

### 🔹 `source`

Defines **where the module comes from**

Examples:

```hcl
source = "./modules/ec2"
source = "terraform-aws-modules/vpc/aws"
source = "git::https://github.com/org/repo.git"
```

---

### 🔹 Versioning (Best Practice)

```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "~> 5.0"
}
```

✔ Prevents breaking changes

---

### 🔹 Module Outputs Usage

```hcl
output "ec2_id" {
  value = module.web_ec2.instance_id
}
```

---

## ❌ What NOT to Do with Modules

❌ Hardcode values inside module
❌ Put provider blocks inside child modules (generally avoid)
❌ Over-complicate small projects
❌ Skip module versioning

---

## 🧠 Modules vs Workspaces (Common Confusion)

| Modules           | Workspaces             |
| ----------------- | ---------------------- |
| Code reuse        | State separation       |
| Logical structure | Environment separation |
| Encouraged always | Use carefully          |

---

## 🎯 Interview-Ready Summary

> **Terraform modules are reusable, self-contained units of infrastructure code that help reduce duplication, enforce standards, and improve maintainability. A Terraform project has a root module and can call multiple child modules, either local or from the Terraform Registry. Modules accept inputs through variables and expose values using outputs.**

---

## 🧠 One-Line Memory Rule

> **Module = reusable infrastructure logic**
