
# 🌱 **What is a Dynamic Block in Terraform?**

A **dynamic block** is used when you need to generate **repeated nested blocks** inside a resource.

Example where nested blocks are repeated:

* `ingress { }` rules in Security Groups
* `egress { }` rules
* `statement { }` in IAM policies
* `listener { }` in ALBs

If you try to loop directly inside the nested block → Terraform does NOT allow it.

So Terraform provides:

```
dynamic "block_name" {
  for_each = ...
  content { ... }
}
```

This dynamically creates multiple nested blocks.

---

# 🌟 **Basic Dynamic Block Syntax**

```hcl
dynamic "nested_block_name" {
  for_each = var.list_or_map
  content {
    # arguments inside nested block
  }
}
```

---

# 🟢 **Example 1: Dynamic Ingress Rules in Security Group (Most Common)**

### 🎯 Goal

Input list of ports → generate multiple `ingress` blocks.

### `variables.tf`

```hcl
variable "ports" {
  type    = list(number)
  default = [22, 80, 443]
}
```

---

### `main.tf`

```hcl
resource "aws_security_group" "web_sg" {
  name = "web-sg"

  dynamic "ingress" {
    for_each = var.ports

    content {
      from_port   = ingress.value
      to_port     = ingress.value
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
    }
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

---

### ✔️ What Terraform Generates Automatically

Terraform expands this into:

```
ingress {
  from_port = 22
  to_port   = 22
  protocol  = "tcp"
}

ingress {
  from_port = 80
  to_port   = 80
  protocol  = "tcp"
}

ingress {
  from_port = 443
  to_port   = 443
  protocol  = "tcp"
}
```

No manual repetition.
Ports are fully dynamic.

---

# 🟣 **Explanation of Each Part**

| Element                | Meaning                                      |
| ---------------------- | -------------------------------------------- |
| `dynamic "ingress"`    | We want to dynamically create ingress blocks |
| `for_each = var.ports` | Loop through ports list                      |
| `ingress.value`        | The current port in the loop                 |
| `content {}`           | Defines what goes inside each nested block   |

---

# 🟠 **Example 2: Dynamic Tags Block (Tags as Nested Blocks)**

Some AWS resources use nested `tag` blocks.

### Input tags:

```hcl
variable "tags" {
  default = {
    Name = "MyServer"
    Env  = "Dev"
    Team = "Ops"
  }
}
```

### Resource using dynamic block:

```hcl
resource "aws_instance" "example" {
  ami           = "ami-123456"
  instance_type = "t2.micro"

  dynamic "tag" {
    for_each = var.tags

    content {
      key   = tag.key
      value = tag.value
    }
  }
}
```

Terraform generates:

```
tag {
  key = "Name"
  value = "MyServer"
}

tag {
  key = "Env"
  value = "Dev"
}

tag {
  key = "Team"
  value = "Ops"
}
```

---

# 🔵 **Example 3: Dynamic Block for IAM Policy Statements**

### Input policy objects:

```hcl
variable "statements" {
  default = [
    {
      actions   = ["s3:ListBucket"]
      resources = ["arn:aws:s3:::mybucket"]
    },
    {
      actions   = ["s3:GetObject"]
      resources = ["arn:aws:s3:::mybucket/*"]
    }
  ]
}
```

### IAM policy with dynamic statements:

```hcl
data "aws_iam_policy_document" "example" {
  dynamic "statement" {
    for_each = var.statements

    content {
      actions   = statement.value.actions
      resources = statement.value.resources
      effect    = "Allow"
    }
  }
}
```

Terraform generates multiple `statement {}` blocks.

---

# 🟩 **Example 4: Dynamic Block for Load Balancer Listener Rules**

```hcl
variable "paths" {
  default = ["/app1", "/app2"]
}

resource "aws_lb_listener_rule" "rules" {
  for_each = toset(var.paths)

  listener_arn = aws_lb_listener.http.arn
  priority     = 100 + index(var.paths, each.value)

  dynamic "condition" {
    for_each = [each.value]

    content {
      path_pattern {
        values = [condition.value]
      }
    }
  }
}
```

Creates one condition per path.

---

# 🎯 When Do You Use Dynamic Blocks?

Use a dynamic block when:

| Situation                                | Dynamic Block Needed?    |
| ---------------------------------------- | ------------------------ |
| You need multiple nested blocks          | ✔️ Yes                   |
| Nested block repeats based on a list     | ✔️ Yes                   |
| You want clean and scalable code         | ✔️ Yes                   |
| Only single block needed                 | ❌ No                     |
| You want to loop at top level (resource) | Use `count` / `for_each` |

---

# 🧠 Why Not Use for_each Directly Inside Nested Block?

Terraform does NOT allow:

```hcl
ingress = each.value
```

Nested blocks must be written literally like:

```
ingress {
  ...
}
```

Dynamic blocks solve this limitation.

---

# 🚀 Summary (Easy to Remember)

| Feature                     | Purpose                             |
| --------------------------- | ----------------------------------- |
| **dynamic block**           | Generates nested blocks dynamically |
| **for_each inside dynamic** | Loop over list/map                  |
| **content {}**              | What nested block contains          |
| **ingress.value / tag.key** | Current loop item                   |

Dynamic blocks are mainly used for:

✔️ Security Groups
✔️ IAM policies
✔️ Load balancer rules
✔️ Tags
✔️ Kubernetes manifests

---


