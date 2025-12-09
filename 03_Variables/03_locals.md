
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

