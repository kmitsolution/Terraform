
# 🔗 Azure VNet Peering – Terraform Example

## 📌 What this example does

✔ Create **Resource Group**
✔ Create **VNet-1 & VNet-2**
✔ Create **Subnets**
✔ Create **VNet Peering (both directions)**
✔ Enable **full communication**

> ⚠️ In Azure, **VNet peering is NOT automatically bidirectional**
> You must create **two peering resources**

---

## 🧠 Azure VNet Peering – Key Concept (Interview MUST-KNOW)

> **Azure VNet peering connects two VNets privately using Microsoft backbone network.**

✔ Low latency
✔ High bandwidth
✔ Works across regions (Global VNet Peering)
❌ No overlapping CIDR blocks

---

## 🏗️ Architecture

```
VNet-1 (10.0.0.0/16)  <==== Peering ====  VNet-2 (10.1.0.0/16)
   |                                       |
 Subnet-1                               Subnet-2
```

---

## 📁 File Structure

```
azure-vnet-peering/
├── main.tf
├── outputs.tf
```

---

## 1️⃣ Provider

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
  name     = "rg-vnet-peering"
  location = "East US"
}
```

---

## 3️⃣ Create VNet-1

```hcl
resource "azurerm_virtual_network" "vnet1" {
  name                = "vnet-1"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  address_space = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "subnet1" {
  name                 = "subnet-1"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet1.name
  address_prefixes     = ["10.0.1.0/24"]
}
```

---

## 4️⃣ Create VNet-2

```hcl
resource "azurerm_virtual_network" "vnet2" {
  name                = "vnet-2"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  address_space = ["10.1.0.0/16"]
}

resource "azurerm_subnet" "subnet2" {
  name                 = "subnet-2"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet2.name
  address_prefixes     = ["10.1.1.0/24"]
}
```

---

## 5️⃣ Create VNet Peering (VNet-1 → VNet-2)

```hcl
resource "azurerm_virtual_network_peering" "vnet1_to_vnet2" {
  name                      = "vnet1-to-vnet2"
  resource_group_name       = azurerm_resource_group.rg.name
  virtual_network_name      = azurerm_virtual_network.vnet1.name
  remote_virtual_network_id = azurerm_virtual_network.vnet2.id

  allow_virtual_network_access = true
  allow_forwarded_traffic      = true
  allow_gateway_transit        = false
}
```

---

## 6️⃣ Create VNet Peering (VNet-2 → VNet-1)

```hcl
resource "azurerm_virtual_network_peering" "vnet2_to_vnet1" {
  name                      = "vnet2-to-vnet1"
  resource_group_name       = azurerm_resource_group.rg.name
  virtual_network_name      = azurerm_virtual_network.vnet2.name
  remote_virtual_network_id = azurerm_virtual_network.vnet1.id

  allow_virtual_network_access = true
  allow_forwarded_traffic      = true
  allow_gateway_transit        = false
}
```

---

## 7️⃣ Outputs

```hcl
output "vnet1_id" {
  value = azurerm_virtual_network.vnet1.id
}

output "vnet2_id" {
  value = azurerm_virtual_network.vnet2.id
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

## 🧠 Very Important Interview Points

### 🔹 Is peering automatically bidirectional?

❌ No
✔ You must create **two peering resources**

---

### 🔹 Are route tables required?

❌ No
Azure automatically handles routing

---

### 🔹 Can CIDR blocks overlap?

❌ No – peering will fail

---

### 🔹 Can peering be cross-region?

✔ Yes (Global VNet Peering)

---

### 🔹 Is traffic encrypted?

✔ Yes (Microsoft backbone)

---

## 🔍 Azure vs AWS Peering (Quick Comparison)

| Feature          | Azure VNet Peering     | AWS VPC Peering |
| ---------------- | ---------------------- | --------------- |
| Routing          | Automatic              | Manual routes   |
| Bidirectional    | ❌ (two peering needed) | ✔               |
| Transitive       | ❌                      | ❌               |
| Overlapping CIDR | ❌                      | ❌               |

---

## 🎯 Interview-Ready Answer

> **Azure VNet peering is implemented using azurerm_virtual_network_peering. Two peering resources are required for bidirectional connectivity. Unlike AWS, Azure automatically manages routing, but CIDR ranges must not overlap. VNet peering allows private communication over Microsoft’s backbone network.**

---

## 🧠 One-Line Memory Rule

> **Azure VNet Peering = Two peerings, no routes needed**

