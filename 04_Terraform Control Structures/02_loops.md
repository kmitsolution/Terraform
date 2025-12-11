
# 🌱 **Loops in Terraform**

Terraform supports loops in three ways:

1. **`count`** → repeats a resource **N times**
2. **`for_each`** → loops through **lists or maps**
3. **`for` expressions** → transforms lists/maps into new lists/maps

Let’s learn each one with easy examples.

---

# 🟦 **1. `count` — Repeat a resource N times**

### ✔️ Use when:

* You want **multiple identical** resources
* You loop using a **number**
* You don’t need unique keys for each item

### 📘 **Example: Create 3 EC2 instances**

```hcl
resource "aws_instance" "server" {
  count         = 3
  ami           = "ami-123456"
  instance_type = "t2.micro"

  tags = {
    Name = "Server-${count.index}"
  }
}
```

### `count.index` values:

| Instance | Name     |
| -------- | -------- |
| 0        | Server-0 |
| 1        | Server-1 |
| 2        | Server-2 |

---

# 🟩 **2. `for_each` — Loop through lists or maps**

### ✔️ Use when:

* Items must have **unique names**
* You need stable addressing (`for_each` prevents recreation)
* You want control over **resource identity**
* You loop through **lists or maps**

---

## 📘 **Example: Create security groups for each port**

### Input list:

```hcl
variable "ports" {
  default = [22, 80, 443]
}
```

### Resource:

```hcl
resource "aws_security_group_rule" "allow_ports" {
  for_each = toset(var.ports)

  type        = "ingress"
  from_port   = each.value
  to_port     = each.value
  protocol    = "tcp"
  cidr_blocks = ["0.0.0.0/0"]
}
```

### `each.value` values:

* 22
* 80
* 443

---

## 📘 **Example with map (key-value)**

```hcl
variable "instances" {
  default = {
    web  = "t2.micro"
    db   = "t3.small"
  }
}

resource "aws_instance" "servers" {
  for_each = var.instances

  ami           = "ami-123456"
  instance_type = each.value

  tags = {
    Name = each.key
  }
}
```

Creates:

| Name | Instance Type |
| ---- | ------------- |
| web  | t2.micro      |
| db   | t3.small      |

---

# 🟨 **3. `for` Expressions — Transform lists or maps**

These are not loops that create resources.
They are used to **build new lists/maps from existing ones**.

### ✔️ Use when:

* You want to transform a list
* You want to filter items
* You want to compute a value

---

## 📘 **Example: Convert list to uppercase**

```hcl
locals {
  names = ["dev", "stage", "prod"]

  upper_names = [for n in local.names : upper(n)]
}
```

Result:

```
["DEV", "STAGE", "PROD"]
```

---

## 📘 **Example: Filter only even numbers**

```hcl
locals {
  numbers = [1, 2, 3, 4, 5, 6]

  even_numbers = [for n in local.numbers : n if n % 2 == 0]
}
```

Result:

```
[2, 4, 6]
```

---

## 📘 **Example: Convert map values**

```hcl
locals {
  instance_map = {
    web  = 1
    app  = 2
    db   = 3
  }

  size_map = { for k, v in local.instance_map : k => v * 10 }
}
```

Result:

```
{
  web = 10
  app = 20
  db  = 30
}
```

---

# 🎯 **When to Use Which Loop?**

| Feature            | `count`             | `for_each`                    | `for` Expression   |
| ------------------ | ------------------- | ----------------------------- | ------------------ |
| Use case           | repeat N times      | loop through list/map         | transform list/map |
| Best for           | identical resources | uniquely identified resources | generating values  |
| Index?             | `count.index`       | `each.key`, `each.value`      | each item          |
| Identity stable?   | ❌ No                | ✔️ Yes                        | ✔️ Yes             |
| Creates resources? | ✔️ Yes              | ✔️ Yes                        | ❌ No               |

---

# 🧠 **Rule of Thumb**

### ✔️ Use **count**

When resources are identical & ordered (0,1,2,…)

### ✔️ Use **for_each**

When resources must be uniquely addressed using keys (like names or maps)

### ✔️ Use **for** expressions

When transforming data into a new list or map

---

# 🏁 Summary Examples (Quick)

### ✔️ count

```hcl
count = 3
```

### ✔️ for_each (list)

```hcl
for_each = toset(var.ports)
```

### ✔️ for_each (map)

```hcl
for_each = var.instances
```

### ✔️ for expression

```hcl
[for x in var.list : upper(x)]
```

---

