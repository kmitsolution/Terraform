
# 🔁 Import Existing EC2 Instance into Terraform

## 🌟 What does `terraform import` do?

> **`terraform import` brings an existing AWS resource into Terraform state so Terraform can start managing it.**

⚠️ Important:

* It **does NOT create resources**
* It **does NOT generate configuration automatically**
* It **only updates the state file**

You must **write the resource block yourself**.

---

# 🧠 High-Level Steps (Remember This)

1️⃣ Identify the existing EC2 instance
2️⃣ Write a matching `aws_instance` block
3️⃣ Run `terraform import`
4️⃣ Run `terraform plan` and fix drift
5️⃣ Terraform now controls the resource

---

# 1️⃣ Get EC2 Instance ID

From AWS Console or CLI:

```bash
aws ec2 describe-instances \
  --query "Reservations[].Instances[].InstanceId"
```

Example:

```
i-0abc12345def67890
```

---

# 2️⃣ Write Terraform Configuration (REQUIRED)

Terraform **will not import without this**.

### `main.tf`

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "existing_ec2" {
  ami           = "ami-068c0051b15cdb816"
  instance_type = "t2.micro"
  key_name      = "ascendion-awskey"

  tags = {
    Name = "Imported-EC2"
  }
}
```

📌 Values don’t have to be perfect yet — we’ll adjust after import.

---

# 3️⃣ Run Terraform Import

### Syntax

```bash
terraform import <resource_type>.<resource_name> <resource_id>
```

### Example

```bash
terraform import aws_instance.existing_ec2 i-0abc12345def67890
```

✔ Terraform now adds the EC2 to **state file**

---

# 4️⃣ Verify Import

```bash
terraform state list
```

Output:

```
aws_instance.existing_ec2
```

Check details:

```bash
terraform state show aws_instance.existing_ec2
```

---

# 5️⃣ Run Terraform Plan (IMPORTANT)

```bash
terraform plan
```

### Possible outcomes:

#### Case 1: ✅ No changes

Perfect — Terraform config matches AWS.

#### Case 2: ⚠️ Drift detected

Terraform shows changes like:

```diff
~ instance_type = "t2.micro" -> "t3.micro"
```

👉 Fix your `.tf` file to match AWS **OR** intentionally change infra.

---

# 🔧 Common Fixes After Import

| Issue            | Fix                          |
| ---------------- | ---------------------------- |
| AMI mismatch     | Update `ami`                 |
| Subnet missing   | Add `subnet_id`              |
| SG missing       | Add `vpc_security_group_ids` |
| Role attached    | Add `iam_instance_profile`   |
| User data exists | Add `user_data`              |
| Tags mismatch    | Match tags                   |

---

# 🚨 Important Limitations (INTERVIEW TRAPS)

❌ Import does NOT create `.tf` code
❌ Import does NOT run `user_data`
❌ Import does NOT fix drift automatically
❌ Import does NOT work without resource block

---

# 🔄 Import Multiple Resources (Example)

```bash
terraform import aws_security_group.web_sg sg-0123456789
terraform import aws_subnet.public subnet-0123456789
terraform import aws_vpc.main vpc-0123456789
```

---

# 🧠 Best Practices (REAL WORLD)

✅ Import **one resource at a time**
✅ Always run `terraform plan` after import
✅ Lock state when importing (S3 + DynamoDB)
✅ Avoid importing into shared workspaces
✅ Document imported resources

---

# 🎯 Interview-Ready Answer

> **Terraform import is used to bring existing cloud resources under Terraform management by adding them to the state file. It requires a matching resource configuration to be defined beforehand. After importing, running terraform plan helps identify drift and align the configuration with the actual infrastructure.**

---

# 🧠 One-Line Memory Rule

> **Import = state only
> Config = your responsibility**

---

