
# 🌱 **What Are Conditional Statements in Terraform?**

Terraform supports conditional expressions using the **ternary operator**:

```
condition ? true_value : false_value
```

This means:

* If **condition is true** → use `true_value`
* If **condition is false** → use `false_value`

You can use conditionals for:
✔️ choosing instance types
✔️ enabling/disabling resources
✔️ selecting optional values
✔️ setting tags
✔️ changing count, for_each, parameters

---

# 🟢 **Basic Syntax**

```hcl
var.env == "prod" ? "t3.large" : "t3.micro"
```

If `env = "prod"` → result = `t3.large`
Otherwise → result = `t3.micro`

---

# 🟦 **Example 1 — Set Instance Type Based on Environment**

### `variables.tf`

```hcl
variable "env" {
  type = string
}
```

### `main.tf`

```hcl
resource "aws_instance" "example" {
  ami           = "ami-123456"
  instance_type = var.env == "prod" ? "t3.large" : "t2.micro"
}
```

### Explanation

* If env = **prod** → big instance
* If env = **dev/stage** → small instance

---

# 🟨 **Example 2 — Enable/Disable a Resource Using count**

Conditional inside `count`:

```hcl
resource "aws_s3_bucket" "logging" {
  count = var.enable_logging ? 1 : 0
  bucket = "my-logging-bucket"
}
```

### Explanation

If `enable_logging = true` → bucket is created
If `enable_logging = false` → bucket is NOT created

This is the most common real-world usage.

---

# 🟩 **Example 3 — Conditional Tag Value**

```hcl
tags = {
  Environment = var.env
  Owner       = var.env == "prod" ? "Admin-Team" : "Dev-Team"
}
```

### Explanation

Prod servers belong to **Admin-Team**, others → **Dev-Team**.

---

# 🟧 **Example 4 — Conditional AMI Selection**

```hcl
ami = var.env == "prod" ? var.prod_ami : var.dev_ami
```

Switches AMI depending on environment.

---

# 🟣 **Example 5 — Conditional Security Group Rule**

```hcl
ingress {
  from_port   = 22
  to_port     = 22
  protocol    = "tcp"
  cidr_blocks = var.env == "prod" ? ["10.0.0.0/16"] : ["0.0.0.0/0"]
}
```

### Explanation

* In **prod** → SSH allowed only inside VPC
* In **dev** → SSH open (not recommended, but demo)

---

# 🔵 **Example 6 — Optional Variable (null Handling)**

Terraform allows:

```hcl
instance_type = var.instance_type != null ? var.instance_type : "t2.micro"
```

Or the shorter version:

```hcl
instance_type = var.instance_type != null ? var.instance_type : "t2.micro"
```

---

# 🧠 **Conditional with Null Values**

Often used to disable attributes:

```hcl
tags = var.add_tags ? local.default_tags : null
```

If you return **null**, Terraform removes that attribute.

---

# 🔥 **Example 7 — Multiple Conditions (nested)**

```hcl
instance_type = var.env == "prod" ? "m5.large" :
                var.env == "stage" ? "t3.medium" :
                "t3.micro"
```

### Result:

* prod → `m5.large`
* stage → `t3.medium`
* otherwise → `t3.micro`

---

# 🎯 **Example 8 — Conditional with for_each**

Disable a resource collection conditionally:

```hcl
resource "aws_security_group_rule" "rules" {
  for_each = var.env == "prod" ? var.prod_rules : {}

  # rule configuration
}
```

If env is not prod → an empty map `{}` results in **no rules created**.

---

# 📝 **Summary Table**

| Purpose               | Example                                          |
| --------------------- | ------------------------------------------------ |
| Select value          | `"prod" ? "A" : "B"`                             |
| Control count         | `count = var.enable ? 1 : 0`                     |
| Select from variables | `var.env == "prod" ? var.prod_ami : var.dev_ami` |
| Apply special tags    | `var.env == "prod" ? "Admin" : "Dev"`            |
| Enable attributes     | `var.enable ? local.x : null`                    |

---

