
# 🌱 **Key Difference (One Line)**

* **Use `count` when you need a fixed number of identical resources.**
* **Use `for_each` when you need resources uniquely identified by keys (names, IDs).**

This is the easiest way to remember it.

---

# 🟦 **1. When to Use `count`**

Use **`count`** when:

### ✔️ You want **N copies** of the same resource

Example: create 3 EC2 instances

```hcl
resource "aws_instance" "server" {
  count         = 3
  ami           = "ami-123"
  instance_type = "t2.micro"
}
```

Creates:

* server[0]
* server[1]
* server[2]

---

### ✔️ Use count when input is a **simple number**

```hcl
count = var.instance_count
```

---

### ✔️ Best for looping through a simple list

```hcl
subnet_id = var.subnet_ids[count.index]
```

---

### ❌ When NOT to use count

Avoid count when:

* Resources need unique names
* List items may change order (causes resource recreation)
* You want stable resource identities

If you remove item 0, then Terraform **renumbers everything** → causes unwanted recreation.

---

# 🟩 **2. When to Use `for_each`**

Use **`for_each`** when:

### ✔️ You need **unique identifiers** (keys)

Input example:

```hcl
variable "users" {
  default = {
    alice = "Admin"
    bob   = "Dev"
  }
}
```

Using `for_each`:

```hcl
resource "aws_iam_user" "user" {
  for_each = var.users
  name     = each.key
  tags = {
    Role = each.value
  }
}
```

This creates:

* user["alice"]
* user["bob"]

Identity is stable.

---

### ✔️ Use for_each when looping through a **map or a set**

```hcl
for_each = var.tags
```

---

### ✔️ Avoids resource recreation

If you delete “bob”, Terraform **only deletes bob**
No reordering → no destructive changes.

---

### ✔️ Best when resources must be uniquely identifiable (by name, ID, or key)

Examples:

* security group rules per port
* IAM users
* DNS records
* Tags
* Subnets

---

# 🟨 **3. Example Comparison (Very Important)**

## 🔸 Using `count`

```hcl
resource "aws_security_group_rule" "rule" {
  count  = length(var.ports)
  from_port = var.ports[count.index]
}
```

If `ports = [22, 80, 443]` → good
But if the order changes → Terraform recreates resources.

---

## 🟢 Using `for_each` (better)

```hcl
resource "aws_security_group_rule" "rule" {
  for_each = toset(var.ports)
  from_port = each.value
}
```

Order doesn't matter.
Each port is treated uniquely and safely.

---

# 🧠 **4. When to Choose Which? (Cheat Sheet)**

| Requirement                                    | Use        |
| ---------------------------------------------- | ---------- |
| Fixed number of identical resources            | `count`    |
| Loop through a list by index                   | `count`    |
| Loop through a list/map with **unique values** | `for_each` |
| Input may change order                         | `for_each` |
| Need stable resource addresses                 | `for_each` |
| Want to reference key/value pairs              | `for_each` |
| Loop creates resources with different settings | `for_each` |

---


---

