
# 🌱 **HCL Syntax Basics (HashiCorp Configuration Language)**

Terraform uses **HCL**, a human-readable language designed for infrastructure.
Its structure is mainly composed of:

1. **Blocks**
2. **Arguments**
3. **Attributes**

Let’s break each one down.

---

# 🧩 **1. Blocks (The main building sections in Terraform)**

A **block** is the core structural unit in HCL.

Example block:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
}
```

A block has:

### **A. Block Type**

`resource`, `provider`, `variable`, `output`, `module`, `locals`, etc.

### **B. Block Labels**

Labels help identify and organize blocks.

Example:

```hcl
resource "aws_instance" "web"
```

* `aws_instance` → resource type
* `"web"` → local name for referencing it

### **C. Body**

Everything inside `{ }`
Includes arguments and attributes.

---

# 🏷️ **2. Arguments (Inputs you provide)**

Arguments are settings you assign inside a block.
They follow the format:

```
key = value
```

Example:

```hcl
instance_type = "t2.micro"
```

### Rules:

* Written inside blocks
* Set values for configuration
* Must match the provider’s schema

---

# 🔄 **3. Attributes (Outputs produced by the resource)**

Attributes are **properties Terraform exposes after a resource is created**.
These come from the actual cloud API.

Example: After creating an EC2 instance, attributes include:

* `id`
* `public_ip`
* `private_ip`
* `arn`

You use them like this:

```hcl
output "instance_ip" {
  value = aws_instance.web.public_ip
}
```

### Summary:

| Item           | Provided by     | Purpose            |
| -------------- | --------------- | ------------------ |
| **Arguments**  | You             | Input to resources |
| **Attributes** | Terraform/cloud | Output properties  |

---

# 🧱 **Putting It All Together**

Example resource block with arguments and attributes:

```hcl
resource "aws_instance" "app" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
}
```

To reference attributes:

```hcl
output "server_id" {
  value = aws_instance.app.id
}
```

---

# 🗂️ **Terraform File Structure**

Terraform doesn't require strict filenames,
but the community follows a **recommended structure**:

---

# 📄 **1. `main.tf` (Core resources file)**

This is where you place:

* AWS/Azure resources
* VPCs
* EC2/VMs
* Security groups
* Storage accounts
* Subnets
* Any infrastructure logic

Example:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
}
```

`main.tf` = central file for your infrastructure code.

---

# 📄 **2. `providers.tf` (Provider configuration file)**

This file defines:

* Providers (AWS, Azure, GCP, Kubernetes, GitHub)
* Authentication method
* Provider-specific settings
* Required provider versions

Example:

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }

  required_version = ">= 1.6.0"
}

provider "aws" {
  region = "us-east-1"
}
```

### Key elements:

* `terraform { }` block → defines required providers
* `provider { }` block → config for each cloud

---

# 📄 Optional but Common Files

### **3. `variables.tf`**

Define input variables.

### **4. `outputs.tf`**

Return useful information after deployment.

### **5. `locals.tf`**

Reusable expressions, naming conventions, computed values.

### **6. `versions.tf`**

Sometimes used separately to declare required Terraform version.

---

# 📚 **Full Sample Project Structure**

```
my-terraform-project/
├── main.tf
├── providers.tf
├── variables.tf
├── outputs.tf
└── terraform.tfstate (auto-created)
```

---

# 🎯 **Quick Summary**

### **HCL Syntax**

* **Blocks** = main structural elements
* **Arguments** = inputs
* **Attributes** = outputs from resources

### **File Structure**

* **main.tf** → core infrastructure
* **providers.tf** → provider config
* **variables.tf** → input variables
* **outputs.tf** → output values

