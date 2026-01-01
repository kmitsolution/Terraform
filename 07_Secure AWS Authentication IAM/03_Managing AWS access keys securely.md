# 🔐 Managing AWS Access Keys Securely

## 🌟 What are AWS Access Keys?

An **AWS access key** consists of:

* **Access Key ID**
* **Secret Access Key**

They are used by:

* AWS CLI
* SDKs
* Terraform
* Automation scripts

❗ These are **long-term credentials** and must be handled carefully.

---

## 🚨 Why Access Keys Are Risky

If leaked, access keys can:

* Delete infrastructure
* Access sensitive data
* Incur huge AWS bills

👉 Many AWS security incidents happen due to **exposed access keys**.

---

# ✅ BEST PRACTICES (MOST IMPORTANT)

## 1️⃣ **Avoid Access Keys When Possible (BEST PRACTICE)**

### ✔ Use **IAM Roles** instead

* For EC2
* Lambda
* ECS
* EKS

### Example:

> EC2 assumes a role → accesses S3
> ❌ No access keys stored
> ❌ No secrets in code

👉 **This is the #1 best practice**

---

## 2️⃣ **Never Hardcode Access Keys**

❌ **Wrong**

```hcl
provider "aws" {
  access_key = "AKIA..."
  secret_key = "abcd..."
}
```

❌ **Wrong**

```bash
export AWS_ACCESS_KEY_ID=AKIA...
```

(committed to scripts or Git)

---

## 3️⃣ **Use Environment Variables (Recommended for Terraform & CLI)**

### ✔ Secure Way

```bash
export AWS_ACCESS_KEY_ID=xxxx
export AWS_SECRET_ACCESS_KEY=yyyy
export AWS_DEFAULT_REGION=us-east-1
```

Terraform automatically picks these up.

✔ No keys in code
✔ Works locally and in CI/CD

---

## 4️⃣ **Use AWS CLI Profiles**

### Configure profile

```bash
aws configure --profile dev
```

### Use in Terraform

```hcl
provider "aws" {
  profile = "dev"
  region  = "us-east-1"
}
```

✔ Clean
✔ Multiple accounts
✔ No hardcoding

---

## 5️⃣ **Use IAM Roles in CI/CD (VERY IMPORTANT)**

For GitHub Actions / Jenkins / GitLab:

✔ Use **OIDC + IAM Role**
✔ No static keys
✔ Temporary credentials

Example:

* GitHub Action assumes IAM Role
* AWS issues short-lived credentials

👉 **Modern & secure**

---

## 6️⃣ **Rotate Access Keys Regularly**

If access keys are unavoidable:

* Rotate every **90 days**
* Delete unused keys
* Only **1 active key per user** if possible

---

## 7️⃣ **Apply Least Privilege**

❌ Don’t give:

```
AdministratorAccess
```

✔ Give only required permissions:

```json
"s3:GetObject"
```

---

## 8️⃣ **Enable MFA for IAM Users**

* Especially for users with access keys
* Adds extra layer of security

---

## 9️⃣ **Store Secrets Securely**

✔ AWS Secrets Manager
✔ AWS SSM Parameter Store
✔ Vault

❌ Not in:

* GitHub
* Terraform variables
* Plain text files

---

## 🔐 Terraform-Specific Best Practices

### ✔ Preferred Authentication Order

1. IAM Role
2. Environment Variables
3. AWS Profile
4. (Last resort) Access keys

### ✔ Example: Secure Terraform Provider

```hcl
provider "aws" {
  region = "us-east-1"
}
```

Terraform automatically uses:

* IAM role (if on EC2)
* Env vars
* Profile

---

## 🚫 What NOT to Do (Interview Traps)

❌ Commit `terraform.tfstate`
❌ Commit `.tfvars` with secrets
❌ Use root account keys
❌ Share access keys via email/Slack
❌ Store keys in GitHub repos

---

## 🎯 Interview-Ready Summary

> **AWS access keys should be avoided whenever possible by using IAM roles. If access keys are required, they should never be hardcoded, must be stored securely using environment variables or profiles, rotated regularly, protected with least privilege and MFA, and never committed to source control. For Terraform and CI/CD, IAM roles and OIDC are the most secure approaches.**

---

## 🧠 One-Line Memory Rule

> **Roles > Environment Variables > Profiles > Access Keys**

