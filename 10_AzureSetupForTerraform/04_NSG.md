---

# 🔐 Azure NSG + Attach to Subnet (Terraform)

## 📌 What this configuration does

* Creates a **Resource Group**
* Creates a **VNet**
* Creates **Public & Private Subnets**
* Creates an **NSG**
* Adds **Inbound rules (SSH + HTTP)**
* Attaches **NSG to a Subnet**

---

## 📁 Recommended Structure

```
azure-nsg/
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

## 2️⃣ Resource Group

```hcl
resource "azurerm_resource_group" "rg" {
  name     = "rg-terraform-nsg"
  location = "East US"
}
```

---

## 3️⃣ Virtual Network

```hcl
resource "azurerm_virtual_network" "vnet" {
  name                = "terraform-vnet"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  address_space = ["10.0.0.0/16"]
}
```

---

## 4️⃣ Subnets

### Public Subnet

```hcl
resource "azurerm_subnet" "public_subnet" {
  name                 = "public-subnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name

  address_prefixes = ["10.0.1.0/24"]
}
```

### Private Subnet

```hcl
resource "azurerm_subnet" "private_subnet" {
  name                 = "private-subnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name

  address_prefixes = ["10.0.2.0/24"]
}
```

---

## 5️⃣ Create Network Security Group (NSG)

```hcl
resource "azurerm_network_security_group" "nsg" {
  name                = "web-nsg"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
}
```

---

## 6️⃣ Add NSG Rules (Inbound)

### Allow SSH (22)

```hcl
resource "azurerm_network_security_rule" "allow_ssh" {
  name                        = "Allow-SSH"
  priority                    = 100
  direction                   = "Inbound"
  access                      = "Allow"
  protocol                    = "Tcp"
  source_port_range           = "*"
  destination_port_range      = "22"
  source_address_prefix       = "*"
  destination_address_prefix  = "*"

  resource_group_name         = azurerm_resource_group.rg.name
  network_security_group_name = azurerm_network_security_group.nsg.name
}
```

### Allow HTTP (80)

```hcl
resource "azurerm_network_security_rule" "allow_http" {
  name                        = "Allow-HTTP"
  priority                    = 110
  direction                   = "Inbound"
  access                      = "Allow"
  protocol                    = "Tcp"
  source_port_range           = "*"
  destination_port_range      = "80"
  source_address_prefix       = "*"
  destination_address_prefix  = "*"

  resource_group_name         = azurerm_resource_group.rg.name
  network_security_group_name = azurerm_network_security_group.nsg.name
}
```

📌 **Priority rule**

* Lower number = higher priority
* Must be unique

---

## 7️⃣ Attach NSG to Subnet (MOST IMPORTANT)

```hcl
resource "azurerm_subnet_network_security_group_association" "public_subnet_nsg" {
  subnet_id                 = azurerm_subnet.public_subnet.id
  network_security_group_id = azurerm_network_security_group.nsg.id
}
```

✔ NSG is now **enforced at subnet level**

---

## 8️⃣ Outputs

```hcl
output "nsg_id" {
  value = azurerm_network_security_group.nsg.id
}

output "public_subnet_id" {
  value = azurerm_subnet.public_subnet.id
}
```

---

## 🏗️ Architecture Diagram (Conceptual)

```
Virtual Network (10.0.0.0/16)
 |
 |-- Public Subnet (10.0.1.0/24)
 |     |
 |     |-- NSG
 |           |-- Allow SSH (22)
 |           |-- Allow HTTP (80)
 |
 |-- Private Subnet (10.0.2.0/24)
```

---

## 🧠 Interview-Important Concepts

### 🔹 What is NSG?

> An **NSG acts as a stateful firewall** that controls inbound and outbound traffic in Azure.

---

### 🔹 Where can NSG be attached?

* ✅ Subnet
* ✅ Network Interface (NIC)

---

### 🔹 Subnet-level vs NIC-level NSG

| Level  | Use Case                  |
| ------ | ------------------------- |
| Subnet | Common rules for many VMs |
| NIC    | VM-specific control       |

---

### 🔹 Default NSG Behavior

* Azure **denies all inbound traffic**
* Allows **VNet internal traffic**
* Allows **outbound internet traffic**

---

## 🎯 Interview-Ready Summary

> **In Terraform, a Network Security Group is created using azurerm_network_security_group and security rules are added using azurerm_network_security_rule. The NSG can then be associated with a subnet using azurerm_subnet_network_security_group_association to control inbound and outbound traffic for all resources in that subnet.**

---

## 🧠 One-Line Memory Rule

> **NSG = firewall
> Rules = traffic control
> Association = enforcement**

