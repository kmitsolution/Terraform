
# 🔐 **Terraform IAM – Users, Roles, and Policies**

## 🌟 Big Picture (Very Important)

Terraform is used to **manage IAM as code**, so that:

* Permissions are **version-controlled**
* Access is **consistent**
* Manual errors are avoided

---

# 1️⃣ **IAM Users in Terraform**

## 🔹 What is an IAM User?

* Represents a **human or application**
* Has **long-term credentials**
* Generally **NOT recommended** for AWS services

---

## ✅ Terraform Example: Create IAM User

```hcl
resource "aws_iam_user" "dev_user" {
  name = "dev-user"
}
```

---

## Attach Policy to User

### Option 1: AWS Managed Policy

```hcl
resource "aws_iam_user_policy_attachment" "user_attach" {
  user       = aws_iam_user.dev_user.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess"
}
```

---

### Option 2: Inline Policy (Not Recommended)

```hcl
resource "aws_iam_user_policy" "inline_policy" {
  name = "inline-s3-policy"
  user = aws_iam_user.dev_user.name

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect   = "Allow"
      Action   = ["s3:ListBucket"]
      Resource = "*"
    }]
  })
}
```

---

## ❌ Why IAM Users are Discouraged

* Long-term access keys
* Security risk
* Hard to rotate credentials

👉 **Use roles instead**

---

# 2️⃣ **IAM Policies in Terraform**

## 🔹 What is an IAM Policy?

A JSON document that defines:

* **Actions**
* **Resources**
* **Effect (Allow/Deny)**

---

## ✅ Create Custom IAM Policy (Best Practice)

```hcl
resource "aws_iam_policy" "s3_policy" {
  name = "custom-s3-policy"

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect   = "Allow"
      Action   = ["s3:GetObject"]
      Resource = "arn:aws:s3:::my-bucket/*"
    }]
  })
}
```

---

## Attach Policy to User / Role

```hcl
resource "aws_iam_role_policy_attachment" "attach_policy" {
  role       = aws_iam_role.ec2_role.name
  policy_arn = aws_iam_policy.s3_policy.arn
}
```

---

# 3️⃣ **IAM Roles in Terraform (MOST IMPORTANT)**

## 🔹 What is an IAM Role?

* Used by **AWS services**
* Has **temporary credentials**
* No access keys
* **Best practice**

---

## ✅ Create IAM Role for EC2

### Trust Policy (Who can assume the role)

```hcl
resource "aws_iam_role" "ec2_role" {
  name = "ec2-s3-role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect = "Allow"
      Principal = {
        Service = "ec2.amazonaws.com"
      }
      Action = "sts:AssumeRole"
    }]
  })
}
```

---

## Attach Policy to Role

```hcl
resource "aws_iam_role_policy_attachment" "attach_s3" {
  role       = aws_iam_role.ec2_role.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess"
}
```

---

## Create Instance Profile (REQUIRED for EC2)

```hcl
resource "aws_iam_instance_profile" "ec2_profile" {
  name = "ec2-instance-profile"
  role = aws_iam_role.ec2_role.name
}
```

---

## Attach Role to EC2

```hcl
resource "aws_instance" "web" {
  ami                  = "ami-068c0051b15cdb816"
  instance_type        = "t2.micro"
  iam_instance_profile = aws_iam_instance_profile.ec2_profile.name
}
```

---

# 🧠 IAM User vs Role vs Policy (INTERVIEW TABLE)

| Concept | Purpose               | Best Practice |
| ------- | --------------------- | ------------- |
| User    | Human access          | ❌ Avoid       |
| Role    | AWS service access    | ✅ Yes         |
| Policy  | Permission definition | ✅ Yes         |

---

# 🔑 IAM Policy Types (Important)

| Policy Type      | Use Case           |
| ---------------- | ------------------ |
| AWS Managed      | Common permissions |
| Customer Managed | Custom permissions |
| Inline Policy    | Small, temporary   |

---

# 🎯 Real-World Scenario (Interview Favorite)

### ❓ How should EC2 access S3?

❌ Access keys
❌ IAM User

✅ **IAM Role attached to EC2**

---

# 🧠 Interview-Ready Summary

> **In Terraform, IAM users represent humans or applications, IAM roles are used by AWS services and provide temporary credentials, and IAM policies define permissions. Best practice is to attach least-privilege policies to roles rather than users, especially for services like EC2. Terraform manages all IAM resources as code for consistency and security.**

---

# 🧠 One-Line Memory Rule

> **Users = people
> Roles = services
> Policies = permissions**

---
