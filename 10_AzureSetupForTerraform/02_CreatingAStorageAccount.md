# ✅ Azure Storage Account Creation using Terraform

## 📌 What this example does

✔ Uses **AzureRM provider**
✔ Creates **Resource Group**
✔ Creates **Storage Account**
✔ Outputs **Storage Account name & primary endpoint**

---

## 📁 Recommended File Structure

```
azure-storage/
├── main.tf
├── variables.tf
├── outputs.tf
```

---

## 1️⃣ Provider Configuration (`main.tf`)

> Authentication is assumed via **Service Principal / Environment Variables**
> (as discussed earlier with Entra ID & RBAC)

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

## 2️⃣ Resource Group (`main.tf`)

```hcl
resource "azurerm_resource_group" "rg" {
  name     = "rg-terraform-storage"
  location = "East US"
}
```

---

## 3️⃣ Storage Account (`main.tf`)

```hcl
resource "azurerm_storage_account" "storage" {
  name                     = "tfstorageacct1234"   # must be globally unique
  resource_group_name      = azurerm_resource_group.rg.name
  location                 = azurerm_resource_group.rg.location

  account_tier             = "Standard"
  account_replication_type = "LRS"

  account_kind             = "StorageV2"
  enable_https_traffic_only = true

  tags = {
    Environment = "Dev"
    CreatedBy  = "Terraform"
  }
}
```

### 🔑 Important Rules for Storage Account Name

* Lowercase only
* 3–24 characters
* Globally unique
* Letters & numbers only

---

## 4️⃣ Variables (Optional) (`variables.tf`)

```hcl
variable "location" {
  default = "East US"
}
```

(You can parameterize the resource group and storage name later.)

---

## 5️⃣ Outputs (`outputs.tf`)

```hcl
output "storage_account_name" {
  value = azurerm_storage_account.storage.name
}

output "primary_blob_endpoint" {
  value = azurerm_storage_account.storage.primary_blob_endpoint
}
```

---

## 6️⃣ Run Terraform

```bash
terraform init
terraform validate
terraform plan
terraform apply
```

---

## 🔍 What Terraform Creates in Azure

```
Subscription
 └── Resource Group (rg-terraform-storage)
      └── Storage Account (tfstorageacct1234)
```

---

## 🧠 Interview-Important Points

### 🔹 Why StorageV2?

* Latest & recommended
* Supports blobs, files, queues, tables

### 🔹 LRS vs GRS

* **LRS**: Cheaper, single region
* **GRS**: Geo-redundant (production)

### 🔹 HTTPS only

* Security best practice
* Enabled by default in Terraform

---

## 🎯 Interview-Ready Summary

> **Using Terraform, an Azure Storage Account is created by first defining a resource group and then using the azurerm_storage_account resource. Key parameters include account tier, replication type, and location. Terraform outputs can be used to expose the storage account name and endpoints for further use.**

---

## 🧠 One-Line Memory Rule

> **Resource Group first → Storage Account next**
