
# 🔐 Attach NSG to NIC (Terraform – Azure)

## 📌 What this example does

✔ Create Resource Group
✔ Create VNet & Subnet
✔ Create NSG with rules
✔ Create NIC
✔ **Attach NSG to NIC**
✔ (Optional) VM can use this NIC

---

## 📁 Recommended Structure

```
azure-nsg-nic/
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
  name     = "rg-nsg-nic"
  location = "East US"
}
```

---

## 3️⃣ Virtual Network & Subnet

```hcl
resource "azurerm_virtual_network" "vnet" {
  name                = "vnet-nsg-nic"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  address_space = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "subnet" {
  name                 = "app-subnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name

  address_prefixes = ["10.0.1.0/24"]
}
```

---

## 4️⃣ Network Security Group (NSG)

```hcl
resource "azurerm_network_security_group" "nsg" {
  name                = "nic-nsg"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
}
```

---

## 5️⃣ NSG Rules (Example)

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

---

## 6️⃣ Create Network Interface (NIC)

```hcl
resource "azurerm_network_interface" "nic" {
  name                = "vm-nic"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  ip_configuration {
    name                          = "internal"
    subnet_id                     = azurerm_subnet.subnet.id
    private_ip_address_allocation = "Dynamic"
  }
}
```

---

## 7️⃣ **Attach NSG to NIC (MOST IMPORTANT)**

```hcl
resource "azurerm_network_interface_security_group_association" "nic_nsg" {
  network_interface_id      = azurerm_network_interface.nic.id
  network_security_group_id = azurerm_network_security_group.nsg.id
}
```

✅ NSG is now enforced **only on this NIC**

---

## 8️⃣ (Optional) Attach NIC to VM

```hcl
resource "azurerm_linux_virtual_machine" "vm" {
  name                = "nsg-nic-vm"
  resource_group_name = azurerm_resource_group.rg.name
  location            = azurerm_resource_group.rg.location
  size                = "Standard_B1s"

  admin_username = "azureuser"

  network_interface_ids = [
    azurerm_network_interface.nic.id
  ]

  admin_ssh_key {
    username   = "azureuser"
    public_key = file("~/.ssh/id_rsa.pub")
  }

  os_disk {
    caching              = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }

  source_image_reference {
    publisher = "Canonical"
    offer     = "0001-com-ubuntu-server-jammy"
    sku       = "22_04-lts"
    version   = "latest"
  }
}
```

---

## 🏗️ Architecture (Conceptual)

```
Virtual Network
 |
 |-- Subnet
 |     |
 |     |-- NIC  <---- NSG
 |           |
 |           |-- VM
```

---

## 🧠 Subnet NSG vs NIC NSG (INTERVIEW MUST-KNOW)

| Level      | Scope                        |
| ---------- | ---------------------------- |
| Subnet NSG | Applies to ALL VMs in subnet |
| NIC NSG    | Applies to ONE VM            |

👉 **Effective rules = Subnet NSG + NIC NSG (combined)**

---

## 🧠 Important Notes

* NSG rules are **stateful**
* Lower priority number = higher priority
* NIC-level NSG overrides are **additive**, not replacing subnet NSG

---

## 🎯 Interview-Ready Summary

> **In Terraform, an NSG can be attached to a Network Interface using the azurerm_network_interface_security_group_association resource. This allows fine-grained security control at the VM level, in addition to or instead of subnet-level NSG enforcement.**

---

## 🧠 One-Line Memory Rule

> **Subnet NSG = group control
> NIC NSG = individual control**
