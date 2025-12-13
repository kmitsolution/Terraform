
## **1. How does Terraform authenticate with Azure?**

Terraform authenticates with Azure using:

* **Azure CLI login** (`az login`)
* **Service Principal** (recommended for CI/CD)
* Managed Identity (advanced)

### Example (Service Principal):

```hcl
provider "azurerm" {
  features {}

  subscription_id = var.subscription_id
  client_id       = var.client_id
  client_secret   = var.client_secret
  tenant_id       = var.tenant_id
}
```

---

## **2. What is the AzureRM provider?**

`azurerm` is the Terraform provider used to manage Azure resources such as:

* Resource groups
* VNets
* Subnets
* VMs
* Storage accounts
* NSGs

```hcl
provider "azurerm" {
  features {}
}
```

---

## **3. What is a Resource Group in Terraform?**

A Resource Group is a logical container for Azure resources.

```hcl
resource "azurerm_resource_group" "rg" {
  name     = "rg-demo"
  location = "East US"
}
```

All Azure resources must belong to a resource group.

---

## **4. How do you create a Virtual Network (VNet) using Terraform?**

```hcl
resource "azurerm_virtual_network" "vnet" {
  name                = "vnet-demo"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
}
```

---

## **5. Difference between Azure ARM templates and Terraform?**

| Feature       | ARM           | Terraform         |
| ------------- | ------------- | ----------------- |
| Cloud support | Azure-only    | Multi-cloud       |
| Language      | JSON/Bicep    | HCL               |
| State         | Azure-managed | Terraform-managed |
| Reusability   | Limited       | Strong (modules)  |

Terraform is more readable and reusable.

---

## **6. What is Azure remote backend in Terraform?**

Terraform stores state in **Azure Storage Account**.

```hcl
terraform {
  backend "azurerm" {
    resource_group_name  = "tfstate-rg"
    storage_account_name = "tfstateaccount"
    container_name       = "tfstate"
    key                  = "dev.terraform.tfstate"
  }
}
```

---

## **7. How do you create a Linux VM in Azure using Terraform?**

```hcl
resource "azurerm_linux_virtual_machine" "vm" {
  name                = "linuxvm"
  resource_group_name = azurerm_resource_group.rg.name
  location            = azurerm_resource_group.rg.location
  size                = "Standard_B1s"
  admin_username      = "azureuser"

  network_interface_ids = [azurerm_network_interface.nic.id]

  admin_ssh_key {
    username   = "azureuser"
    public_key = file("~/.ssh/id_rsa.pub")
  }
}
```

---

## **8. What is a Network Security Group (NSG)?**

NSG controls inbound and outbound traffic.

```hcl
resource "azurerm_network_security_group" "nsg" {
  name                = "nsg-demo"
  location            = azurerm_resource_group.rg.location
}
```

NSGs are similar to AWS Security Groups.

---

## **9. How do you attach an NSG to a subnet?**

```hcl
resource "azurerm_subnet_network_security_group_association" "assoc" {
  subnet_id                 = azurerm_subnet.subnet.id
  network_security_group_id = azurerm_network_security_group.nsg.id
}
```

---

## **10. What are data sources in Azure Terraform?**

Data sources read existing Azure resources.

```hcl
data "azurerm_resource_group" "existing" {
  name = "existing-rg"
}
```

Used when resources already exist.

---

## **11. What is `features {}` block in AzureRM provider?**

The `features {}` block is mandatory and enables Azure-specific behaviors.

```hcl
provider "azurerm" {
  features {}
}
```

Without it, Terraform fails.

---

## **12. How do you manage multiple environments in Azure Terraform?**

Common methods:

* Separate `.tfvars` files
* Terraform workspaces
* Separate backend keys

Example:

```bash
terraform apply -var-file=prod.tfvars
```

---

## **13. How does Terraform handle Azure credentials in CI/CD?**

Using:

* Service Principal
* Environment variables:

```bash
export ARM_CLIENT_ID=...
export ARM_CLIENT_SECRET=...
export ARM_SUBSCRIPTION_ID=...
export ARM_TENANT_ID=...
```

---

## **14. What is the difference between `count` and `for_each` in Azure Terraform?**

Same as AWS:

* `count` → simple repetition
* `for_each` → unique resources

Azure resources strongly prefer `for_each` for stability.

---

## **15. How do you delete Azure resources created by Terraform?**

```bash
terraform destroy
```

Terraform deletes only the resources tracked in the state file.

---

