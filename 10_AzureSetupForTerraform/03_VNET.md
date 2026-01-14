# 🌐 Azure VNET & Subnet using Terraform

## 📌 What this example covers

✔ Create **Resource Group**
✔ Create **Virtual Network (VNet)**
✔ Create **Multiple Subnets**
✔ Follow **Azure networking best practices**

---

## 📁 Recommended Folder Structure

```
azure-vnet/
├── main.tf
├── variables.tf
├── outputs.tf
```

---

## 1️⃣ Provider Configuration (`main.tf`)

> Authentication assumed via **Service Principal / Environment Variables**

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

## 2️⃣ Resource Group

```hcl
resource "azurerm_resource_group" "rg" {
  name     = "rg-terraform-network"
  location = "East US"
}
```

---

## 3️⃣ Create Virtual Network (VNet)

```hcl
resource "azurerm_virtual_network" "vnet" {
  name                = "terraform-vnet"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  address_space = ["10.0.0.0/16"]

  tags = {
    Environment = "Dev"
  }
}
```

### 🔑 Key points

* `address_space` defines the **VNet CIDR**
* VNet is a **logical network boundary**

---

## 4️⃣ Create Subnet – Public Subnet

```hcl
resource "azurerm_subnet" "public_subnet" {
  name                 = "public-subnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name

  address_prefixes = ["10.0.1.0/24"]
}
```

---

## 5️⃣ Create Subnet – Private Subnet

```hcl
resource "azurerm_subnet" "private_subnet" {
  name                 = "private-subnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name

  address_prefixes = ["10.0.2.0/24"]
}
```

---

## 6️⃣ Outputs (`outputs.tf`)

```hcl
output "vnet_id" {
  value = azurerm_virtual_network.vnet.id
}

output "public_subnet_id" {
  value = azurerm_subnet.public_subnet.id
}

output "private_subnet_id" {
  value = azurerm_subnet.private_subnet.id
}
```

---

## 7️⃣ Run Terraform

```bash
terraform init
terraform validate
terraform plan
terraform apply
```

---

## 🏗️ Architecture Diagram (Conceptual)

```
Azure Subscription
 └── Resource Group
      └── Virtual Network (10.0.0.0/16)
           ├── Public Subnet  (10.0.1.0/24)
           └── Private Subnet (10.0.2.0/24)
```

---

## 🧠 Interview-Important Concepts

### 🔹 What is a VNet?

> A **VNet** is a logically isolated network in Azure used to securely connect resources.

---

### 🔹 What is a Subnet?

> A **Subnet** divides a VNet into smaller address ranges for better isolation and control.

---

### 🔹 Can subnets overlap?

❌ No
Azure **does not allow overlapping CIDRs**

---

### 🔹 Why multiple subnets?

* Security isolation
* Different routing rules
* NSG / Firewall separation

---

## 🎯 Interview-Ready Summary

> **Using Terraform, an Azure Virtual Network is created by defining an address space and deploying subnets within it. Subnets segment the network for security and traffic control. Terraform manages VNets and subnets using the azurerm_virtual_network and azurerm_subnet resources inside a resource group.**

---

## 🧠 One-Line Memory Rule

> **VNet = network boundary
> Subnet = network segmentation**
