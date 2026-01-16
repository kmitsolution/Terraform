# 🔌 Azure NIC `ip_configuration` (Private IP + Public IP)

## 🧠 Concept First (Very Important)

In Azure:

* **Private IP** → Assigned in NIC `ip_configuration`
* **Public IP** → Separate resource, then **attached to NIC**
* **NIC is the attachment point**, not the VM directly

👉 **Flow**

```
VNet → Subnet → NIC → (Private IP + Public IP) → VM
```

---

## 📌 What this example does

✔ Create Resource Group
✔ Create VNet & Subnet
✔ Create **Public IP resource**
✔ Create **NIC with ip_configuration**
✔ Attach **Private + Public IP to NIC**

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
  name     = "rg-nic-ipconfig"
  location = "East US"
}
```

---

## 3️⃣ VNet & Subnet

```hcl
resource "azurerm_virtual_network" "vnet" {
  name                = "vnet-ipconfig"
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

## 4️⃣ Public IP Resource (MANDATORY for Public Access)

```hcl
resource "azurerm_public_ip" "pip" {
  name                = "vm-public-ip"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  allocation_method = "Static"
  sku               = "Standard"

  tags = {
    Environment = "Dev"
  }
}
```

### 🔑 Important

* **Standard SKU** → Recommended
* **Static allocation** → Stable public IP

---

## 5️⃣ Network Interface (NIC) with `ip_configuration`

```hcl
resource "azurerm_network_interface" "nic" {
  name                = "vm-nic"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  ip_configuration {
    name                          = "primary"
    subnet_id                     = azurerm_subnet.subnet.id
    private_ip_address_allocation = "Dynamic"
    public_ip_address_id          = azurerm_public_ip.pip.id
  }
}
```

### 🔍 What happens here

* Azure assigns a **private IP** from subnet
* Public IP is **mapped to the private IP**
* NIC becomes internet-accessible

---

## 6️⃣ (Optional) Attach NIC to VM

```hcl
resource "azurerm_linux_virtual_machine" "vm" {
  name                = "vm-ipconfig"
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

## 🧠 Interview-Important Points

### 🔹 Where is the private IP defined?

✅ In **NIC `ip_configuration`**

---

### 🔹 Where is the public IP defined?

✅ As a **separate resource**, attached to NIC

---

### 🔹 Can a NIC have multiple IP configurations?

✅ Yes

Example:

```hcl
ip_configuration { ... }
ip_configuration { ... }
```

---

### 🔹 Can VM exist without public IP?

✅ Yes (private subnet workloads)

---

### 🔹 Dynamic vs Static Private IP

| Type    | Usage                   |
| ------- | ----------------------- |
| Dynamic | Default, most cases     |
| Static  | Domain controllers, DNS |

---

## 🔍 Azure vs AWS (Quick Comparison)

| Concept      | Azure                | AWS        |
| ------------ | -------------------- | ---------- |
| Private IP   | NIC ip_configuration | ENI        |
| Public IP    | Separate resource    | Elastic IP |
| Attachment   | NIC                  | ENI        |
| Subnet level | Required             | Required   |

---

## 🎯 Interview-Ready Answer

> **In Azure, both private and public IPs are configured at the Network Interface level. The private IP is assigned within the NIC’s ip_configuration block, while the public IP is created as a separate resource and attached to the NIC. The VM then consumes the NIC, not the IPs directly.**

---

