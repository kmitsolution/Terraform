# 🌍 Using Environment Variables in Terraform

## 🌟 What are Environment Variables?

> **Environment variables allow you to pass values to Terraform securely without hardcoding them in `.tf` files.**

Terraform automatically reads specific environment variables and uses them during execution.

---

## 🔑 Why Use Environment Variables?

✔ Avoid hardcoding secrets
✔ More secure than `.tfvars`
✔ Ideal for CI/CD pipelines
✔ Easy to rotate credentials
✔ No secrets in GitHub

---

# 1️⃣ **Using Environment Variables for Terraform Variables**

Terraform follows this pattern:

```
TF_VAR_<variable_name>
```

---

## ✅ Example: Sensitive Variable via Environment Variable

### **variables.tf**

```hcl
variable "db_password" {
  type      = string
  sensitive = true
}
```

### **Set environment variable**

#### Linux / macOS

```bash
export TF_VAR_db_password="MySecretPassword123"
```

#### Windows (PowerShell)

```powershell
$env:TF_VAR_db_password="MySecretPassword123"
```

### **Use in resource**

```hcl
resource "aws_db_instance" "db" {
  engine   = "mysql"
  username = "admin"
  password = var.db_password
}
```

✔ Terraform automatically picks it up
✔ Value is hidden in output

---

# 2️⃣ **Using Environment Variables for AWS Authentication (VERY IMPORTANT)**

Terraform automatically uses AWS environment variables.

---

## ✅ AWS Credentials via Environment Variables (Best Practice)

### Linux / macOS

```bash
export AWS_ACCESS_KEY_ID=AKIAxxxx
export AWS_SECRET_ACCESS_KEY=abcdxxxx
export AWS_DEFAULT_REGION=us-east-1
```

### Windows (PowerShell)

```powershell
$env:AWS_ACCESS_KEY_ID="AKIAxxxx"
$env:AWS_SECRET_ACCESS_KEY="abcdxxxx"
$env:AWS_DEFAULT_REGION="us-east-1"
```

### **Terraform Provider**

```hcl
provider "aws" {
  region = "us-east-1"
}
```

❗ No credentials in Terraform code
❗ No credentials in `.tfvars`

---

# 3️⃣ **Using AWS Profiles via Environment Variables**

```bash
export AWS_PROFILE=dev
```

Terraform uses that profile automatically.

```hcl
provider "aws" {
  region = "us-east-1"
}
```

---

# 4️⃣ **Using Environment Variables in CI/CD (Most Common)**

### Example: GitHub Actions / Jenkins

```bash
export AWS_ACCESS_KEY_ID=xxxx
export AWS_SECRET_ACCESS_KEY=yyyy
export TF_VAR_env=prod
```

Terraform runs without any secrets in code.

---

# 5️⃣ **Precedence Order (Important for Interview)**

Terraform variable priority (highest → lowest):

1️⃣ `-var` CLI flag
2️⃣ `TF_VAR_` environment variables
3️⃣ `.tfvars` files
4️⃣ Default values

---

# 6️⃣ **When NOT to Use Environment Variables**

❌ Large structured data (use `.tfvars`)
❌ Long-term secret storage (use Secrets Manager)
❌ Hardcoding inside scripts

---

# 7️⃣ **Best Practices (REAL WORLD)**

✅ Prefer **IAM roles** over keys
✅ Use env vars for **CI/CD**
✅ Combine with **sensitive = true**
✅ Never commit secrets
✅ Rotate credentials

---

# ⚠️ Common Mistakes (Interview Traps)

❌ Thinking env vars are encrypted
❌ Printing env vars in logs
❌ Committing `.tfvars`
❌ Using root access keys
❌ Forgetting to unset variables

---

# 🎯 Interview-Ready Summary

> **Terraform supports environment variables for both input variables and provider authentication. Variables prefixed with `TF_VAR_` are automatically loaded, and AWS credentials can be securely passed using standard AWS environment variables. This approach avoids hardcoding secrets and is widely used in CI/CD pipelines.**

---

# 🧠 One-Line Memory Rule

> **TF_VAR_ → Terraform variables
> AWS_* → Provider authentication**

---

