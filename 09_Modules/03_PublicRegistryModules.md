
# 🌍 Public Terraform Registry Modules

## 🌟 What is the Public Terraform Registry?

> The **Public Terraform Registry** is an official repository maintained by HashiCorp that hosts **reusable, versioned Terraform modules and providers**.

📍 Website: **[https://registry.terraform.io](https://registry.terraform.io)**

It contains:

* AWS, Azure, GCP modules
* Kubernetes, Helm, networking modules
* Community + official modules

---

## 📦 What is a Public Registry Module?

> A **public registry module** is a reusable Terraform module published to the Terraform Registry that can be consumed directly using a `source` reference.

✔ Pre-built
✔ Versioned
✔ Well-documented
✔ Community-reviewed

---

## 🧠 Why Use Public Modules?

Without public modules:

* Write everything from scratch
* More bugs
* Slower delivery

With public modules:

* 🚀 Faster infrastructure creation
* ✅ Best practices built-in
* ♻️ Reusable across teams
* 📦 Maintained by experts

---

## 🏗️ Module Naming Convention (Important)

```
<namespace>/<name>/<provider>
```

### Example:

```hcl
terraform-aws-modules/vpc/aws
```

| Part                  | Meaning      |
| --------------------- | ------------ |
| terraform-aws-modules | Organization |
| vpc                   | Module name  |
| aws                   | Provider     |

---

## 🔧 How to Use a Public Registry Module

### Example: Create VPC using Public Module

```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "5.1.0"

  name = "my-vpc"
  cidr = "10.0.0.0/16"

  azs             = ["us-east-1a", "us-east-1b"]
  public_subnets  = ["10.0.1.0/24", "10.0.2.0/24"]
  private_subnets = ["10.0.3.0/24", "10.0.4.0/24"]

  enable_nat_gateway = true
  single_nat_gateway = true
}
```

✔ No need to create IGW, NAT, routes manually
✔ Production-ready networking

---

## 🔐 Versioning (VERY IMPORTANT)

Always pin a version:

```hcl
version = "~> 5.0"
```

Why?

* Avoid breaking changes
* Predictable behavior
* Safer upgrades

---

## 📤 Accessing Outputs from Public Modules

```hcl
output "vpc_id" {
  value = module.vpc.vpc_id
}
```

Public modules expose **many useful outputs**.

---

## 🧠 How to Evaluate a Public Module (Interview Point)

Before using a module, check:

✔ Number of downloads
✔ Last updated date
✔ Maintainers
✔ Documentation quality
✔ Examples provided
✔ Versioning strategy

---

## ⚠️ When NOT to Use Public Modules

❌ Highly custom infrastructure
❌ Tight security constraints
❌ Learning internals (for beginners)
❌ When module hides too much logic

In such cases → **local or custom modules**

---

## 🧠 Public vs Local Modules

| Feature     | Public Module      | Local Module            |
| ----------- | ------------------ | ----------------------- |
| Location    | Terraform Registry | Local filesystem        |
| Versioning  | Built-in           | Manual                  |
| Reusability | Global             | Project-level           |
| Best for    | Production, speed  | Learning, customization |

---

## 🔒 Security Best Practices

✅ Review module code
✅ Pin versions
✅ Avoid untrusted publishers
✅ Limit permissions via inputs
✅ Combine with remote backend

---

## 🎯 Interview-Ready Summary

> **Public Terraform Registry modules are reusable, versioned modules published on the Terraform Registry that provide production-ready infrastructure components. They help teams accelerate development by following best practices, but should always be used with version pinning and proper review.**

---

## 🧠 One-Line Memory Rule

> **Public modules = reuse trusted infrastructure fast**

