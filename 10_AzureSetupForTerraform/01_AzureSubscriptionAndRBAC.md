# 🔷 Azure Subscription & RBAC for Terraform

## 🌟 Big Picture

> **Terraform needs permission to create, update, and delete Azure resources.**
> In Azure, these permissions are controlled using:

* **Azure Subscription**
* **Azure RBAC (Role-Based Access Control)**

Terraform **does NOT use usernames/passwords**.
It uses a **Service Principal** with RBAC permissions.

---

# 1️⃣ Azure Subscription (Foundation)

## 🔹 What is an Azure Subscription?

> An **Azure Subscription** is a **billing + management boundary** where Azure resources are created.

It contains:

* Resource Groups
* VMs, VNets, Storage, etc.
* RBAC assignments
* Billing info

📌 **Terraform always works inside a subscription**

---

## 🔹 How Terraform Knows Which Subscription to Use

Terraform uses:

* `subscription_id`
* Provided via **Service Principal authentication**

Example:

```hcl
provider "azurerm" {
  features {}
  subscription_id = var.subscription_id
}
```

---

## 🔹 List Azure Subscriptions (CLI)

```bash
az account list -o table
```

Set active subscription:

```bash
az account set --subscription "<SUBSCRIPTION_ID>"
```

---

# 2️⃣ Azure RBAC (Role-Based Access Control)

## 🔐 What is Azure RBAC?

> **RBAC controls who can do what on which Azure resources.**

RBAC is based on:

* **Security principal** (user, group, service principal)
* **Role**
* **Scope**

---

## 🧱 RBAC Core Components

### 1️⃣ Security Principal

* User
* Group
* **Service Principal (used by Terraform)**

---

### 2️⃣ Role (Permission Set)

Common built-in roles:

| Role                      | Permissions                    |
| ------------------------- | ------------------------------ |
| Owner                     | Full access + RBAC             |
| Contributor               | Create/update/delete resources |
| Reader                    | Read-only                      |
| User Access Administrator | Manage RBAC                    |

📌 **Terraform usually needs `Contributor`**

---

### 3️⃣ Scope (Where permissions apply)

RBAC can be applied at:

* Management Group
* **Subscription**
* Resource Group
* Resource

Example scopes:

```text
/subscriptions/<sub-id>
/subscriptions/<sub-id>/resourceGroups/my-rg
```

---

# 3️⃣ Service Principal (Terraform Identity)

## 🔹 What is a Service Principal?

> A **Service Principal** is an application identity used by Terraform to authenticate to Azure.

Terraform:

* Logs in as Service Principal
* Uses RBAC to perform actions

---

## 🔧 Create Service Principal for Terraform

```bash
az ad sp create-for-rbac \
  --name terraform-sp \
  --role Contributor \
  --scopes /subscriptions/<SUBSCRIPTION_ID>
```

### Output:

```json
{
  "appId": "xxxx",
  "password": "yyyy",
  "tenant": "zzzz"
}
```

---

## 🔑 Required Values for Terraform

| Terraform Variable | Azure Value           |
| ------------------ | --------------------- |
| client_id          | appId                 |
| client_secret      | password              |
| tenant_id          | tenant                |
| subscription_id    | Azure subscription ID |

---

# 4️⃣ Terraform Provider Configuration (Azure)

```hcl
provider "azurerm" {
  features {}

  subscription_id = var.subscription_id
  client_id       = var.client_id
  client_secret   = var.client_secret
  tenant_id       = var.tenant_id
}
```

✔ This is how Terraform authenticates using RBAC

---

# 5️⃣ Using Environment Variables (BEST PRACTICE)

Instead of hardcoding:

### Linux / macOS

```bash
export ARM_SUBSCRIPTION_ID="xxxx"
export ARM_CLIENT_ID="xxxx"
export ARM_CLIENT_SECRET="xxxx"
export ARM_TENANT_ID="xxxx"
```

### Windows PowerShell

```powershell
$env:ARM_SUBSCRIPTION_ID="xxxx"
$env:ARM_CLIENT_ID="xxxx"
$env:ARM_CLIENT_SECRET="xxxx"
$env:ARM_TENANT_ID="xxxx"
```

Terraform automatically detects these.

---

# 6️⃣ Minimum RBAC Required for Terraform

### Recommended:

* **Contributor** at:

  * Subscription level (simple)
  * OR Resource Group level (more secure)

Example (Resource Group scope):

```bash
az role assignment create \
  --assignee <appId> \
  --role Contributor \
  --scope /subscriptions/<sub-id>/resourceGroups/my-rg
```

---

# 7️⃣ Azure Subscription + RBAC Flow (Terraform)

```
Terraform
   |
   |-- Service Principal
   |
   |-- Authenticates with Azure AD
   |
   |-- RBAC checks permissions
   |
   |-- Creates resources in Subscription
```

---

# 8️⃣ Common Mistakes (Interview Traps)

❌ Using personal Azure login for Terraform
❌ Assigning Owner role unnecessarily
❌ Hardcoding secrets in `.tf` files
❌ Wrong subscription selected
❌ Missing RBAC at correct scope

---

# 🎯 Interview-Ready Summary

> **Terraform accesses Azure using a Service Principal that is assigned RBAC permissions within a subscription. The subscription defines the boundary where resources are created, and RBAC controls what Terraform is allowed to do. Best practice is to grant the Service Principal the Contributor role at the resource group or subscription level and authenticate using environment variables.**

---

# 🧠 One-Line Memory Rule

> **Subscription = where
> RBAC = what
> Service Principal = who**



Thanks for sharing this. I’ll **explain what you’ve done, what is correct, what needs improvement, and the best-practice way** to use this with Terraform.

⚠️ **Important security note first**
You have pasted a **real client secret**. Treat it as **compromised** and **rotate it immediately** in Entra ID → App Registration → Certificates & Secrets.

---

## 1️⃣ What you did (Correct Flow)

You followed the **right Azure → Terraform authentication approach**:

### Azure side

```
Entra ID
 └── App Registrations
     └── New App (terraform)
         ├── Application (client) ID
         ├── Directory (tenant) ID
         └── Client Secret
```

This App Registration acts as a **Service Principal**, which Terraform uses to authenticate.

✔ Correct
✔ Industry standard
✔ Interview-ready approach

---

## 2️⃣ Mapping Azure Values → Terraform Provider

| Azure Portal            | Terraform         |
| ----------------------- | ----------------- |
| Application (client) ID | `client_id`       |
| Client Secret (Value)   | `client_secret`   |
| Directory (tenant) ID   | `tenant_id`       |
| Subscription ID         | `subscription_id` |

Your mapping is **correct**.

---

## 3️⃣ Your Terraform Code (What It Does)

### Terraform required provider

```hcl
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "4.57.0"
    }
  }
}
```

✔ Locks provider version
✔ Prevents breaking changes

---

### Provider configuration

```hcl
provider "azurerm" {
  subscription_id = "4a273469-e273-459d-9e83-85de18ff7be2"
  client_id       = "e8d8716f-1b9a-442a-b88b-8db1e08d3733"
  client_secret   = "****"
  tenant_id       = "caec7360-0562-4b3d-be4b-b7c913928b33"
  features {}
}
```

✔ Terraform authenticates using **Service Principal**
✔ RBAC decides what it can do

---

### Resource creation

```hcl
resource "azurerm_resource_group" "name" {
  name     = "myResourceGroup"
  location = "East US"
}
```

Terraform will:

* Login to Azure using App Registration
* Check RBAC permissions
* Create Resource Group

---

## 4️⃣ What Is MISSING (Very Important)

### ❌ No RBAC role assigned

Creating an App Registration **alone is not enough**.

You MUST assign a role to the Service Principal.

### Required role

✅ **Contributor**

### Where to assign

* Subscription level (simple)
* OR Resource Group level (least privilege)

---

## 5️⃣ Assign RBAC Role (MANDATORY)

### Option 1: Azure Portal

1. Go to **Subscription**
2. Access control (IAM)
3. Add role assignment
4. Role: **Contributor**
5. Assign access to: **User, group, or service principal**
6. Select: `terraform` app

---

### Option 2: Azure CLI (Recommended)

```bash
az role assignment create \
  --assignee e8d8716f-1b9a-442a-b88b-8db1e08d3733 \
  --role Contributor \
  --scope /subscriptions/4a273469-e273-459d-9e83-85de18ff7be2
```

---

## 6️⃣ BEST PRACTICE (Do NOT Hardcode Secrets)

Your current code **works**, but it is **NOT secure**.

### ❌ Avoid this

```hcl
client_secret = "plain-text-secret"
```

---

## ✅ Correct & Secure Way (Environment Variables)

### Windows (PowerShell)

```powershell
$env:ARM_SUBSCRIPTION_ID="4a273469-e273-459d-9e83-85de18ff7be2"
$env:ARM_CLIENT_ID="e8d8716f-1b9a-442a-b88b-8db1e08d3733"
$env:ARM_CLIENT_SECRET="new-secret-value"
$env:ARM_TENANT_ID="caec7360-0562-4b3d-be4b-b7c913928b33"
```

### Linux / macOS

```bash
export ARM_SUBSCRIPTION_ID=...
export ARM_CLIENT_ID=...
export ARM_CLIENT_SECRET=...
export ARM_TENANT_ID=...
```

Then your provider becomes:

```hcl
provider "azurerm" {
  features {}
}
```

✔ No secrets in code
✔ Safe for GitHub
✔ CI/CD ready

---

## 7️⃣ Authentication Flow (Terraform + Azure)

```
Terraform
  |
  |-- App Registration (Service Principal)
  |
  |-- Azure AD Authentication
  |
  |-- RBAC Permission Check
  |
  |-- Create Resource Group
```

---

## 8️⃣ Common Errors & Fixes

### ❌ Error: AuthorizationFailed

👉 RBAC role not assigned or wrong scope

### ❌ Error: Invalid client secret

👉 Secret expired or copied incorrectly

### ❌ Error: Subscription not found

👉 Wrong subscription_id or not assigned access

---

## 🎯 Interview-Ready Explanation

> **Terraform authenticates to Azure using an Entra ID App Registration (Service Principal). The Service Principal is granted RBAC permissions, typically the Contributor role, at the subscription or resource group level. Terraform then uses these credentials to create and manage Azure resources securely.**

---

