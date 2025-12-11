
# 🌱 **What Is Provisioning in Terraform?**

Provisioning = The process of **configuring** a server **after it is created**.

Terraform supports two styles:

1. **Static Provisioning** → Using `user_data`, cloud-init, metadata
2. **Dynamic Provisioning** → Using Terraform `provisioners` (remote-exec, local-exec, file)

---

# 🟢 **1. Static Provisioning (Recommended)**

Static provisioning means:

✔️ Configuration happens **during boot** of the machine
✔️ No SSH required
✔️ No Terraform connection block
✔️ More reliable, scalable, idempotent
✔️ Works without public IP
✔️ Cloud-native method

Terraform sends the configuration to cloud provider → cloud executes it.

---

## ⭐ **Examples of Static Provisioning**

---

## **Example 1 — AWS EC2 user_data**

```hcl
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t2.micro"

  user_data = <<-EOF
    #!/bin/bash
    yum update -y
    yum install -y httpd
    systemctl start httpd
  EOF
}
```

### ✔️ Runs **during instance boot**

No SSH needed.

---

## **Example 2 — Azure cloud-init**

```hcl
resource "azurerm_linux_virtual_machine" "vm" {
  name                = "myvm"
  admin_username      = "azureuser"
  
  custom_data = filebase64("cloud-init.yaml")
}
```

### ✔️ cloud-init config runs automatically on VM startup

---

## ⭐ Static Provisioning Advantages

| Benefit                   | Explanation                     |
| ------------------------- | ------------------------------- |
| No SSH needed             | No remote connection required   |
| Highly reliable           | Runs at boot → predictable      |
| Works in private networks | No public IP needed             |
| Cloud-native              | Best practice for AWS/Azure/GCP |
| Scales well               | For 100s of servers             |

---

# 🔵 **2. Dynamic Provisioning (Not Recommended unless necessary)**

Dynamic provisioning means:

✔️ Terraform runs commands **after VM is created**
✔️ Uses **provisioners**
✔️ Requires **SSH connection**
✔️ Failure can cause taint
✔️ Not always reliable
✔️ Used only when user_data cannot handle the task

---

## ⭐ Dynamic Provisioning Types

### 1) **remote-exec**

Run commands on remote server via SSH.

```hcl
provisioner "remote-exec" {
  inline = ["sudo yum install -y httpd"]
}
```

---

### 2) **local-exec**

Run commands on your local machine.

```hcl
provisioner "local-exec" {
  command = "echo Server Created >> output.txt"
}
```

---

### 3) **file**

Upload file to VM.

```hcl
provisioner "file" {
  source      = "config.txt"
  destination = "/tmp/config.txt"
}
```

---

## ⭐ Dynamic Provisioning Disadvantages

| Issue                | Explanation                                      |
| -------------------- | ------------------------------------------------ |
| Requires SSH         | Must open port 22 → not secure                   |
| Failure causes taint | Terraform marks resource as corrupted            |
| Hard to maintain     | Commands inside provisioners get messy           |
| Not idempotent       | Running `terraform apply` twice may break things |
| Not cloud-native     | Terraform team discourages heavy use             |

---

# 🟣 **Why Static > Dynamic?**

| Feature                  | Static (user_data) | Dynamic (remote-exec)    |
| ------------------------ | ------------------ | ------------------------ |
| Reliability              | ⭐⭐⭐⭐⭐              | ⭐⭐                       |
| Requires SSH             | ❌                  | ✔️                       |
| Cloud best-practice      | ✔️                 | ❌                        |
| Works in private subnets | ✔️                 | ❌ (unless using bastion) |
| Idempotent               | ✔️                 | ❌                        |
| Scale                    | Easy               | Difficult                |

---

# 🏆 **When to Use Static Provisioning**

Use **static provisioning** when:

✔️ Installing common software
✔️ Bootstrap scripts
✔️ Cloud-init / user_data scripts
✔️ Initial configuration at boot
✔️ Scaling groups (Auto Scaling, VMSS)
✔️ Immutable infrastructure patterns

---

# 🔥 **When to Use Dynamic Provisioning**

Only use **dynamic provisioning** when static provisioning cannot do the job:

✔️ You must upload files *after* instance is running
✔️ You must run a command that depends on another Terraform resource
✔️ You must run external scripts (Ansible, bash, PowerShell)
✔️ Short-term testing in development environments

---

# 🎯 **Easy Example Showing Difference**

## Static:

```hcl
user_data = <<EOF
#!/bin/bash
yum install -y httpd
EOF
```

Runs during EC2 boot.

---

## Dynamic:

```hcl
provisioner "remote-exec" {
  inline = ["sudo yum install -y httpd"]
}
```

Runs after EC2 is created & SSH is ready.

---

# 🧠 **Interview-Ready Summary**

| Static Provisioning         | Dynamic Provisioning |
| --------------------------- | -------------------- |
| Uses user_data / cloud-init | Uses provisioners    |
| No SSH needed               | SSH required         |
| Runs at boot                | Runs after creation  |
| Reliable                    | Error-prone          |
| Cloud-native                | Last resort          |
| Scalable                    | Not scalable         |

**Terraform Recommended = Static Provisioning**

