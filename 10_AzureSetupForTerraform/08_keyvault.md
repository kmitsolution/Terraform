
# 🔐 Azure Key Vault with Secret using Terraform

## 📌 What this setup does

✔ Create Resource Group
✔ Create Azure Key Vault
✔ Enable RBAC authorization
✔ Grant access to current user / Service Principal
✔ Create a **Key Vault Secret**

---

## 🏗️ Architecture

```
Terraform
  |
  |-- Azure Resource Group
        |
        |-- Key Vault
              |
              |-- Secret (password / key / token)
```

---

## 📁 File Structure

```
azure-keyvault/
├── main.tf
├── variables.tf
├── outputs.tf
```

---

## 1️⃣ Provider Configuration

```hcl
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "4.57.0"
    }
  }
}

provider "azurerm" {
  features {}
}
```

---

## 2️⃣ Get Current Client (IMPORTANT)

Terraform needs your **tenant & object ID**.

```hcl
data "azurerm_client_config" "current" {}
```

---

## 3️⃣ Resource Group

```hcl
resource "azurerm_resource_group" "rg" {
  name     = "rg-keyvault"
  location = "East US"
}
```

---

## 4️⃣ Create Key Vault (RBAC Enabled – Recommended)

```hcl
resource "azurerm_key_vault" "kv" {
  name                       = "kv-terraform-demo123"
  location                   = azurerm_resource_group.rg.location
  resource_group_name        = azurerm_resource_group.rg.name
  tenant_id                  = data.azurerm_client_config.current.tenant_id

  sku_name = "standard"

  enable_rbac_authorization = true

  soft_delete_retention_days = 7
  purge_protection_enabled  = false
}
```

### 🔑 Key Points

* **RBAC is preferred** over access policies
* Name must be **globally unique**
* Soft delete enabled by default

---

## 5️⃣ Assign RBAC Role to Create Secrets

Terraform identity must have permission.

```hcl
resource "azurerm_role_assignment" "kv_secrets_officer" {
  scope                = azurerm_key_vault.kv.id
  role_definition_name = "Key Vault Secrets Officer"
  principal_id         = data.azurerm_client_config.current.object_id
}
```

📌 Required to create secrets.

---

## 6️⃣ Create a Key Vault Secret

```hcl
resource "azurerm_key_vault_secret" "example" {
  name         = "db-password"
  value        = "SuperSecretPassword123!"
  key_vault_id = azurerm_key_vault.kv.id

  depends_on = [
    azurerm_role_assignment.kv_secrets_officer
  ]
}
```

✔ Secret is encrypted
✔ Stored securely
✔ Not visible in plain text in Azure Portal

---

## 7️⃣ Outputs

```hcl
output "key_vault_name" {
  value = azurerm_key_vault.kv.name
}

output "secret_name" {
  value = azurerm_key_vault_secret.example.name
}
```

---

## 🚀 Run Terraform

```bash
terraform init
terraform plan
terraform apply
```

---

## 🔍 Verify in Azure Portal

1. Go to **Key Vault**
2. Secrets → `db-password`
3. Value is hidden by default

---

## 🧠 Very Important Security Notes

### ❌ Don’t hardcode secrets in real projects

Instead use:

* `terraform.tfvars`
* Environment variables
* Azure DevOps / GitHub secrets
* External secret injection

Example:

```hcl
value = var.db_password
```

---

## 🧠 Access Policy vs RBAC (INTERVIEW MUST-KNOW)

| Feature                | Access Policy | RBAC |
| ---------------------- | ------------- | ---- |
| Legacy                 | ✅             | ❌    |
| Azure recommended      | ❌             | ✅    |
| Centralized IAM        | ❌             | ✅    |
| Terraform future-proof | ❌             | ✅    |

---

## 🎯 Interview-Ready Summary

> **In Terraform, Azure Key Vault is created using azurerm_key_vault. Secrets are stored using azurerm_key_vault_secret. Modern implementations use Azure RBAC instead of access policies, requiring roles like Key Vault Secrets Officer to manage secrets securely.**

---

## 🧠 One-Line Memory Rule

> **Key Vault = secure store
> RBAC = access control
> Secret = encrypted value**

---

