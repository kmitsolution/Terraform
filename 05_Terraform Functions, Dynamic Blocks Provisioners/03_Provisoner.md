
✔️ `remote-exec`
✔️ `local-exec`
✔️ `file` provisioner

Provisioners are used **after** a resource is created to run commands or copy files — usually for initial setup (bootstrap), not long-term configuration.

---

# 🌱 **What Are Provisioners in Terraform?**

Provisioners allow you to:

* Run commands **on the local machine**
* Run commands **on a remote machine (EC2/VM)**
* Upload files to a remote machine

Provisioners run **after the resource is created**, during the `terraform apply`.

> **Important:** Provisioners should be used only when necessary. Prefer cloud-init or user_data when possible.

---

# 🟦 **1. remote-exec Provisioner**

Used to run commands **inside a remote server** (EC2, VM, etc.).

Requirements:

* SSH connection (Linux)
* WinRM (Windows)
* Instance must be reachable (SG + key + public IP)

---

## ✅ **Example: Install Apache on EC2 Using remote-exec**

### Resource with provisioner:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0123456789abcdef0"
  instance_type = "t2.micro"
  key_name      = "mykey"

  provisioner "remote-exec" {
    inline = [
      "sudo yum update -y",
      "sudo yum install -y httpd",
      "sudo systemctl start httpd",
      "sudo systemctl enable httpd"
    ]
  }

  connection {
    type        = "ssh"
    user        = "ec2-user"
    private_key = file("mykey.pem")
    host        = self.public_ip
  }
}
```

---

## ✔️ What happens?

After EC2 is created:

1. Terraform connects via **SSH**
2. Runs the listed commands
3. Installs & starts Apache web server

---

## ✔️ `inline` vs `script`

Inline commands:

```hcl
inline = ["sudo yum install -y httpd"]
```

Run a script file:

```hcl
script = "install_apache.sh"
```

---

# 🟩 **2. local-exec Provisioner**

Runs commands **on the local machine** (your laptop or CI/CD server), not on EC2.

Useful for:

* triggering scripts
* sending notifications
* running Ansible
* updating local files
* calling APIs

---

## ✅ **Example: Save EC2 public IP locally**

```hcl
resource "aws_instance" "example" {
  ami           = "ami-123456"
  instance_type = "t2.micro"

  provisioner "local-exec" {
    command = "echo ${self.public_ip} >> iplist.txt"
  }
}
```

### ✔️ Output:

Terraform appends the EC2 instance public IP into a file on your computer.

---

## Another Example: Run Ansible after server creation

```hcl
provisioner "local-exec" {
  command = "ansible-playbook -i ${self.public_ip}, playbook.yml"
}
```

---

# 🟧 **3. File Provisioner**

Uploads a **file or directory** from your local machine → remote instance.

---

## 3.1 Upload a Single File

```hcl
provisioner "file" {
  source      = "app.conf"
  destination = "/etc/myapp/app.conf"
}
```

---

## 3.2 Upload a folder/directory

```hcl
provisioner "file" {
  source      = "scripts/"
  destination = "/tmp/scripts/"
}
```

---

## File provisioner requires connection block:

```hcl
connection {
  type        = "ssh"
  user        = "ec2-user"
  private_key = file("mykey.pem")
  host        = self.public_ip
}
```

---

# 🧠 **Provisioner Execution Order**

If multiple provisioners exist:

```
file → remote-exec → local-exec
```

But every provisioner runs **after resource creation**.

---

# ⚠️ Important Notes

✔️ Provisioners should be **last resort**
Prefer:

* `user_data` for AWS EC2
* `cloud-init`
* Configuration management tools (Ansible, Chef, Puppet)

✔️ Provisioners run **only during creation**, not updates unless you taint/destroy/replace the resource.

✔️ If provisioning fails → Terraform marks the resource as **tainted**.

---

# 🎯 Summary Table

| Provisioner     | Runs On                     | Use Case                                 |
| --------------- | --------------------------- | ---------------------------------------- |
| **remote-exec** | Remote server               | Install software, configure system       |
| **local-exec**  | Local machine               | Run scripts, trigger Ansible, log output |
| **file**        | Uploads from local → remote | Copy config files, scripts               |

---


Which one should we cover next?
