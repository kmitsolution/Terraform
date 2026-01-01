# 🔐 **AWS IAM – Fundamentals**

## 🌟 What is IAM?

**IAM (Identity and Access Management)** is an AWS service that controls:

* **Who** can access AWS (identity)
* **What** they can access (permissions)
* **How** they access it (authentication method)

👉 IAM is **global** (not region-specific).

---

## 🧩 Core IAM Components

### 1️⃣ **Users**

An **IAM User** represents a **human or application**.

* Has **username**
* Can have:

  * Password (AWS Console)
  * Access Keys (CLI / SDK)

📌 Example:

```text
User: dev-user
Access: S3, EC2
```

---

### 2️⃣ **Groups**

A **Group** is a collection of users.

* Permissions are assigned to groups
* Users inherit group permissions
* Easier to manage than individual users

📌 Example:

```text
Group: Developers
Permissions: EC2FullAccess
Users: alice, bob
```

---

### 3️⃣ **Roles** (VERY IMPORTANT)

A **Role** is an identity **assumed temporarily**.

* No permanent credentials
* Used by:

  * EC2
  * Lambda
  * ECS
  * Cross-account access

📌 Example:

```text
EC2 assumes role → Access S3
```

👉 **Best practice** for services (NOT users).

---

### 4️⃣ **Policies**

A **Policy** defines permissions in **JSON** format.

Two types:

* **AWS Managed Policy** (pre-built)
* **Customer Managed Policy** (custom)

📌 Policies are attached to:

* Users
* Groups
* Roles

---

## 📜 IAM Policy Structure (VERY IMPORTANT)

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "*"
    }
  ]
}
```

### Key Elements:

| Element   | Meaning               |
| --------- | --------------------- |
| Effect    | Allow or Deny         |
| Action    | AWS API action        |
| Resource  | AWS resource ARN      |
| Condition | Optional restrictions |

---

## 🧠 IAM Authentication vs Authorization

| Concept        | Meaning                     |
| -------------- | --------------------------- |
| Authentication | Who are you?                |
| Authorization  | What are you allowed to do? |

IAM handles **both**.

---

## 🔑 IAM Access Methods

| Method              | Used For     |
| ------------------- | ------------ |
| Console login       | Humans       |
| Access Key + Secret | CLI / SDK    |
| Role assumption     | AWS services |

---

## 🧱 IAM Best Practices (INTERVIEW MUST-KNOW)

✅ Use **roles** for EC2/Lambda
✅ Apply **least privilege**
✅ Do NOT use root account
✅ Enable **MFA**
✅ Use **groups** for permissions
✅ Rotate access keys
✅ Avoid hardcoded credentials

---

## 🔄 IAM Role vs User (Common Interview Question)

| Feature               | User | Role |
| --------------------- | ---- | ---- |
| Permanent credentials | ✅    | ❌    |
| Used by humans        | ✅    | ❌    |
| Used by AWS services  | ❌    | ✅    |
| Best practice         | ❌    | ✅    |

---

## 🌍 Real-World Example

### EC2 accessing S3 (Correct Way)

1. Create IAM Role
2. Attach S3 policy
3. Attach role to EC2
4. EC2 accesses S3 **without keys**

❌ Do NOT use access keys on EC2.

---

## 🧠 IAM + Terraform (High-Level)

In Terraform:

* `aws_iam_user`
* `aws_iam_role`
* `aws_iam_policy`
* `aws_iam_role_policy_attachment`

IAM is often **managed via Terraform** for consistency.

---

## 🎯 Interview-Ready Summary

> **IAM is AWS’s identity and access management service used to control authentication and authorization. It consists of users, groups, roles, and policies. Best practice is to use roles with least-privilege policies, especially for AWS services like EC2 and Lambda, instead of long-term access keys.**

---

## 🧠 One-Line Memory Rule

> **Users = people
> Roles = services
> Policies = permissions**

