
# 🌱 **What Are Locals in Terraform?**

**Locals** allow you to define **reusable values** inside your Terraform code.

You create a `locals` block where you store:

* computed values
* reusable expressions
* standard naming formats
* shortcuts to long expressions

Locals help avoid repeating the same logic everywhere.

---

# 🧩 **Basic Syntax**

```hcl
locals {
  name_prefix = "myapp"
  instance_size = "t2.micro"
}
```

You use them like:

```hcl
var.instance_type = local.instance_size
```

---

# 🎯 **Why Use Locals?**

✔️ Avoid repeating the same value in many places
✔️ Make code cleaner and readable
✔️ Compute values once, reuse everywhere
✔️ Centralize naming conventions
✔️ Combine multiple variables into one expression

---

# 🟢 **Simple Example**

### `locals.tf`

```hcl
locals {
  app_name = "webapp"
  env      = "dev"
}
```

### Use in a resource:

```hcl
tags = {
  Name = "${local.app_name}-${local.env}"
}
```

Output:

```
webapp-dev
```

---

# 🧩 **Example 2: Combining Variables with Locals**

### variables.tf

```hcl
variable "project" { type = string }
variable "environment" { type = string }
```

### locals.tf

```hcl
locals {
  full_name = "${var.project}-${var.environment}"
}
```

### main.tf

```hcl
resource "aws_s3_bucket" "example" {
  bucket = local.full_name
}
```

If variables are:

```
project = "payment"
environment = "prod"
```

Bucket name becomes:

```
payment-prod
```

---

# 🧩 **Example 3: Locals for Naming Standards**

```hcl
locals {
  common_tags = {
    Owner = "DevOps-Team"
    ManagedBy = "Terraform"
  }
}
```

Use in multiple resources:

```hcl
tags = local.common_tags
```

---

# 🧩 **Example 4: Locals for Computed Values**

```hcl
locals {
  instance_name = "${var.project}-${var.env}-${var.role}"
  is_production = var.env == "prod"
}
```

Use:

```hcl
count = local.is_production ? 3 : 1
```

---

# 🧩 **Example 5: Locals to Avoid Repeating Long Expressions**

Instead of repeating:

```hcl
var.vpc.subnets.private.ids[0]
```

You define:

```hcl
locals {
  private_subnet = var.vpc.subnets.private.ids[0]
}
```

Use:

```hcl
subnet_id = local.private_subnet
```

---

# 🧩 **Example 6: Complex Local**

```hcl
locals {
  server_config = {
    name = "appserver"
    size = "t3.medium"
    tags = {
      Env = var.environment
    }
  }
}
```

Use:

```hcl
instance_type = local.server_config.size
tags          = local.server_config.tags
```

---

# 🎯 Summary

| Feature      | Description                                              |
| ------------ | -------------------------------------------------------- |
| **Purpose**  | Define reusable expressions                              |
| **Used for** | Common names, tags, computed values, avoiding repetition |
| **Access**   | `local.name`                                             |
| **Location** | Typically in `locals.tf`                                 |

---

# 🌱 **Difference Between Variables and Locals**

| Feature                | Variables                              | Locals                                                |
| ---------------------- | -------------------------------------- | ----------------------------------------------------- |
| **Purpose**            | Inputs provided **from outside**       | Internal reusable values created **inside** Terraform |
| **Can user override?** | YES (via tfvars, CLI)                  | NO (locals cannot be changed externally)              |
| **Use case**           | Values that may differ per environment | Computed values, naming, reusable logic               |
| **File**               | `variables.tf`                         | `locals.tf`                                           |
| **Access**             | `var.variable_name`                    | `local.name`                                          |

---

# 🎯 **WHEN TO USE VARIABLES**

Use **variables** when the value should be provided by:

* user
* pipeline
* tfvars file
* environment
* module input

Variables = **inputs**

### ✔️ Use variables for:

* instance type
* region
* environment (`dev/prod`)
* VPC CIDR
* number of servers
* database size
* any config that changes between environments

### Example:

```hcl
variable "instance_type" {
  type = string
}
```

Why?
Because **instance_type is not constant**—dev and prod use different sizes.

---

# 🎯 **WHEN TO USE LOCALS**

Use **locals** when:

* The value should NOT be overridden
* You are building a computed expression
* You want to avoid repeating the same logic
* You want to create naming conventions
* You want cleaner code

Locals = **internal reusable expressions**

### ✔️ Use locals for:

* naming format (`"${var.project}-${var.env}"`)
* combining variables
* computed values
* reusable tags
* derived values
* shortcuts for complex expressions

### Example:

```hcl
locals {
  name_prefix = "${var.project}-${var.env}"
}
```

Why?
Because this is a **calculated value**, not a user input.

---

# 🌟 **Side-by-Side Example**

### ❌ WRONG (using variable for calculated value)

```hcl
variable "instance_name" {
  default = "${var.project}-${var.env}"
}
```

Reason: It's not an input; it's a computation.

---

### ✔️ CORRECT (use locals)

```hcl
locals {
  instance_name = "${var.project}-${var.env}"
}
```

Use it:

```hcl
tags = {
  Name = local.instance_name
}
```

---

# 🧠 **Easy Rule to Remember**

### ✔️ **Use VARIABLES for values that users/environment must pass.**

Examples:

* region
* environment
* instance_type
* port number
* CIDR blocks

---

### ✔️ **Use LOCALS for values computed from those variables.**

Examples:

* naming standards
* tags
* expressions
* combinations
* formatting (`upper(var.env)`)

---

# 🚀 **Practical Real-World Example**

### variables.tf

```hcl
variable "project" {}
variable "env" {}
variable "region" {}
```

### locals.tf

```hcl
locals {
  name_prefix = "${var.project}-${var.env}"
  common_tags = {
    Project = var.project
    Env     = var.env
  }
}
```

### main.tf

```hcl
resource "aws_s3_bucket" "example" {
  bucket = local.name_prefix

  tags = local.common_tags
}
```

Here:

* `project`, `env`, `region` → **user input**
* `name_prefix`, `common_tags` → **calculated values**

---

# 🎯 Final Summary

| Use                         | VARIABLES           | LOCALS              |
| --------------------------- | ------------------- | ------------------- |
| Input from user             | ✔️ Yes              | ❌ No                |
| Internal calculation        | ❌ Rarely            | ✔️ Always           |
| Environment-specific values | ✔️                  | ❌                   |
| Naming convention           | ❌                   | ✔️                  |
| Reusable tags               | ❌                   | ✔️                  |
| Can be overridden?          | ✔️ Yes              | ❌ Never             |
| Example                     | `var.instance_type` | `local.name_prefix` |

---

