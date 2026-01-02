
# 🔐 Sensitive Variables in Terraform

## 🌟 What are Sensitive Variables?

> **Sensitive variables are used to store secrets** (passwords, tokens, keys) and **prevent Terraform from printing their values** in CLI output.

Examples:

* Database passwords
* API tokens
* AWS secret access keys
* Private keys

---

## ✅ How to Define a Sensitive Variable

```hcl
variable "db_password" {
  type      = string
  sensitive = true
}
```

✔ Terraform hides the value in `plan` and `apply`
✔ Still usable inside resources

---

## ✅ How to Pass Sensitive Values

### 1️⃣ Environment Variables (BEST PRACTICE)

```bash
export TF_VAR_db_password="MySecret123"
```

Windows (PowerShell):

```powershell
$env:TF_VAR_db_password="MySecret123"
```

✔ No secrets in code
✔ Ideal for CI/CD

---

### 2️⃣ `.tfvars` file

```hcl
db_password = "MySecret123"
```

⚠️ **Do NOT commit `.tfvars` to Git**

---

### 3️⃣ CLI (NOT recommended)

```bash
terraform apply -var="db_password=MySecret123"
```

❌ Exposed in shell history

---

## ✅ Using Sensitive Variable in a Resource

```hcl
resource "aws_db_instance" "db" {
  engine   = "mysql"
  username = "admin"
  password = var.db_password
}
```

Terraform output will show:

```
password = (sensitive value)
```

---

## 🔍 Important Truth (INTERVIEW TRAP)

> **`sensitive = true` does NOT encrypt data**

Sensitive values are still stored in:

* `terraform.tfstate`
* Remote backend (S3, Terraform Cloud)

👉 Security depends on **state protection**

---

## 🔐 Protecting Sensitive Data (REAL WORLD)

### ✔ Use Encrypted Remote Backend

```hcl
terraform {
  backend "s3" {
    bucket  = "tf-state-bucket"
    key     = "prod/terraform.tfstate"
    region  = "us-east-1"
    encrypt = true
  }
}
```

---

## ✅ Sensitive Outputs

```hcl
output "db_password" {
  value     = var.db_password
  sensitive = true
}
```

✔ Output hidden
✔ Still accessible via state

---

## ❌ Common Mistakes

❌ Hardcoding secrets in `.tf` files
❌ Committing `.tfvars` or state file
❌ Assuming sensitive = encrypted
❌ Printing secrets in outputs
❌ Using access keys instead of IAM roles

---

## 🏆 Best Practice Hierarchy

1️⃣ IAM Roles (no secrets at all)
2️⃣ AWS Secrets Manager / SSM
3️⃣ Environment variables
4️⃣ Sensitive variables
5️⃣ Hardcoded values ❌

---

## 🎯 Interview-Ready Summary

> **Sensitive variables in Terraform prevent secret values from being displayed in CLI output, but they do not encrypt data. Secrets are still stored in the state file, so best practice is to combine sensitive variables with encrypted remote state and external secret managers or IAM roles whenever possible.**

---

## 🧠 One-Line Memory Rule

> **Sensitive hides output — backend secures data**
