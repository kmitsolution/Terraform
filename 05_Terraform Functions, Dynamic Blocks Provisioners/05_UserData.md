
# 🚀 **What is `user_data` in EC2?**

> **`user_data` is a bootstrap script that runs automatically when an EC2 instance starts for the first time.**

✔ Runs **inside EC2**
✔ Runs **at boot time**
✔ Does **NOT require SSH**
✔ Does **NOT require remote-exec**
✔ **Best practice** for provisioning

Terraform simply **passes the script to AWS**, and AWS executes it.

---

# ✅ **Basic Syntax in Terraform**

```hcl
resource "aws_instance" "example" {
  user_data = <<EOF
    #!/bin/bash
    echo "Hello World"
  EOF
}
```

---

# 🧱 **FULL REAL-WORLD EXAMPLE (Install Apache using user_data)**

### 🎯 Use case

* Launch EC2
* Install Apache
* Start & enable Apache
* No SSH, no PEM, no timeout

---

## **main.tf**

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_security_group" "web_sg" {
  name = "allow-http"

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_instance" "web" {
  ami                    = "ami-068c0051b15cdb816"   # Amazon Linux
  instance_type          = "t2.micro"
  key_name               = "ascendion-awskey"
  vpc_security_group_ids = [aws_security_group.web_sg.id]

  user_data = <<-EOF
    #!/bin/bash
    yum install -y httpd
    systemctl start httpd
    systemctl enable httpd
    echo "Hello from Terraform user_data" > /var/www/html/index.html
  EOF

  tags = {
    Name = "UserData-EC2"
  }
}
```

---

# 🔍 **What Happens Internally**

1. Terraform creates EC2
2. AWS passes the script to EC2
3. EC2 runs the script **during boot**
4. Apache is installed automatically

👉 No SSH
👉 No `remote-exec`
👉 No `.pem` permissions needed

---

# 🪟 **How to Verify user_data Worked**

Open browser:

```
http://<EC2-PUBLIC-IP>
```

You’ll see:

```
Hello from Terraform user_data
```

---

# 📂 **Using External Script File (Best Practice)**

Instead of inline script, keep it clean.

### `userdata.sh`

```bash
#!/bin/bash
yum install -y httpd
systemctl start httpd
systemctl enable httpd
```

### Terraform:

```hcl
resource "aws_instance" "web" {
  user_data = file("userdata.sh")
}
```

---

# 🧠 **Important Notes (Interview MUST-KNOW)**

### 🔹 Runs only:

* On **first boot**
* If instance is recreated

To re-run:

```bash
terraform taint aws_instance.web
terraform apply
```

---

# ⚠️ **user_data vs remote-exec (VERY IMPORTANT)**

| Feature                 | user_data | remote-exec    |
| ----------------------- | --------- | -------------- |
| Runs when               | Boot time | After creation |
| Needs SSH               | ❌ No      | ✅ Yes          |
| Needs PEM               | ❌ No      | ✅ Yes          |
| Timeout issues          | ❌ No      | ✅ Yes          |
| Works in private subnet | ✅ Yes     | ❌ No           |
| Recommended             | ⭐⭐⭐⭐⭐     | ⭐⭐             |

👉 **Terraform best practice = user_data**

---

