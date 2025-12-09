
### **1. Define variables in `variables.tf`**

```hcl
variable "instance_type" {
  type = string
}

variable "environment" {
  type = string
}

variable "cpu_count" {
  type = number
}
```

---

### **2. Create a simple `dev.tfvars` file**

```hcl
instance_type = "t2.micro"
environment   = "dev"
cpu_count     = 2
```

---

### **3. Use variables in `main.tf`**

```hcl
resource "aws_instance" "example" {
  ami           = "ami-123456"
  instance_type = var.instance_type

  tags = {
    Environment = var.environment
    CPU         = var.cpu_count
  }
}
```

---

### **4. Apply using TFVARS file**

```bash
terraform apply -var-file=dev.tfvars
```

---

# 🎉 Output example after apply:

```
instance_type = "t2.micro"
environment   = "dev"
cpu_count     = 2
```

---

