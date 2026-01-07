# 📦 Terraform State File Commands

## 🌟 What is Terraform State?

The **Terraform state file (`terraform.tfstate`)**:

* Keeps track of **real infrastructure**
* Maps **Terraform resources → cloud resources**
* Is critical for **plan, apply, destroy**

---

## 🔑 Core Terraform State Commands

---

## 1️⃣ `terraform state list`

### 👉 What it does

Lists **all resources tracked in the state file**.

### Example

```bash
terraform state list
```

### Output

```
aws_vpc.main
aws_instance.web
aws_security_group.web_sg
```

### When to use

✔ Verify what Terraform manages
✔ After `terraform import`
✔ Debug missing resources

---

## 2️⃣ `terraform state show`

### 👉 What it does

Shows **detailed attributes** of a resource **from the state file**.

### Example

```bash
terraform state show aws_instance.web
```

### When to use

✔ Check current values
✔ Debug drift
✔ Verify IAM role, subnet, SG, etc.

---

## 3️⃣ `terraform state pull`

### 👉 What it does

Downloads the **current state file** and prints it as JSON.

### Example

```bash
terraform state pull
```

### When to use

✔ Debug state issues
✔ Backup state manually
✔ Inspect remote backend state

⚠️ Contains sensitive data — **handle carefully**

---

## 4️⃣ `terraform state push`

### 👉 What it does

Uploads a **local state file** to the backend.

### Example

```bash
terraform state push terraform.tfstate
```

### When to use

✔ Manual state recovery
✔ Restore corrupted state

⚠️ Dangerous if misused — overwrites backend state

---

## 5️⃣ `terraform state mv`

### 👉 What it does

Moves or renames a resource **inside the state file** without changing infrastructure.

### Example (rename resource)

```bash
terraform state mv aws_instance.old aws_instance.new
```

### Example (move to module)

```bash
terraform state mv aws_instance.web module.ec2.aws_instance.web
```

### When to use

✔ Refactoring Terraform code
✔ Renaming resources
✔ Moving to modules

---

## 6️⃣ `terraform state rm`

### 👉 What it does

Removes a resource **from state only**, NOT from AWS.

### Example

```bash
terraform state rm aws_instance.web
```

### When to use

✔ Stop Terraform managing a resource
✔ Resource deleted manually in AWS
✔ Fix broken state

⚠️ After this, Terraform **forgets the resource**

---

## 7️⃣ `terraform refresh`

### 👉 What it does

Updates state to match **real infrastructure**.

### Example

```bash
terraform refresh
```

### When to use

✔ Detect drift
✔ Sync state after manual changes

⚠️ Deprecated in favor of:

```bash
terraform plan -refresh-only
```

---

## 8️⃣ `terraform plan -refresh-only`

### 👉 What it does

Refreshes state **without making changes**.

### Example

```bash
terraform plan -refresh-only
```

### When to use

✔ Safely sync state
✔ Verify drift before apply

---

## 9️⃣ `terraform import`

### 👉 What it does

Imports an **existing resource into Terraform state**.

### Example

```bash
terraform import aws_instance.web i-0123456789
```

### When to use

✔ Bring manually created resources under Terraform
✔ Start managing existing infra

❗ Requires resource block in `.tf` file

---

## 🔐 State Locking (Important)

When using remote backend (S3 + DynamoDB):

* Terraform locks state during apply
* Prevents concurrent modifications

---

## 🧠 Common State File Scenarios

| Scenario               | Command                |
| ---------------------- | ---------------------- |
| See managed resources  | `terraform state list` |
| Inspect a resource     | `terraform state show` |
| Rename resource        | `terraform state mv`   |
| Stop managing resource | `terraform state rm`   |
| Sync drift             | `plan -refresh-only`   |
| Import existing infra  | `terraform import`     |

---

## 🚨 State File Best Practices (INTERVIEW MUST-KNOW)

✅ Use **remote backend (S3)**
✅ Enable **encryption**
✅ Enable **DynamoDB locking**
❌ Never commit `terraform.tfstate`
❌ Avoid manual edits
❌ Avoid `state push` unless necessary

---

## 🎯 Interview-Ready Summary

> **Terraform state commands are used to inspect, modify, and manage the Terraform state file. Commands like state list and show help inspect resources, state mv helps refactor without recreation, state rm removes resources from state without deleting them, and import brings existing resources under Terraform management. Proper state handling is critical for safe infrastructure management.**

---

## 🧠 One-Line Memory Rule

> **State commands change Terraform’s memory, not AWS directly**

