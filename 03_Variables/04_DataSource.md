## DataSource in Terraform
A data source in Terraform is used to read existing information from AWS without creating any resources.

Examples of read-only information:

Latest AMI ID

Existing EC2 instance details

VPC IDs

Security groups

EBS volume IDs

```
   image-id

  instance-type

  tag:Name

  instance.group-id

  vpc-id
```
A resource ➝ creates
A data source ➝ reads

# 🟢 **EXAMPLE 1 — Fetch Latest Amazon Linux AMI Using Data Source + Create EC2**

## ✅ **Full Code First**

### `provider.tf`

```hcl
provider "aws" {
  region = "us-east-1"
}
```

---

### `main.tf`

```hcl
data "aws_ami" "awslinux" {
  most_recent = true
  owners      = ["amazon"]

  filter {
    name   = "name"
    values = ["amzn2-ami-hvm*"]
  }

  filter {
    name   = "root-device-type"
    values = ["ebs"]
  }

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }

  filter {
    name   = "architecture"
    values = ["x86_64"]
  }
}

resource "aws_instance" "ins1" {
  ami           = data.aws_ami.awslinux.id
  instance_type = "t2.micro"
  key_name      = "newkey"
}
```

---

### `outputs.tf`

```hcl
output "selected_ami_id" {
  value = data.aws_ami.awslinux.id
}

output "instance_public_ip" {
  value = aws_instance.ins1.public_ip
}
```

---

## 📝 **Explanation**

### ✔️ What this data source does

`aws_ami` **reads** the latest Amazon Linux 2 AMI by filtering:

| Filter           | Meaning                  |
| ---------------- | ------------------------ |
| `amzn2-ami-hvm*` | Only Amazon Linux 2 AMIs |
| `ebs`            | EBS-backed AMIs          |
| `hvm`            | Modern virtualization    |
| `x86_64`         | 64-bit architecture      |

`most_recent = true` ensures Terraform picks the **latest image**.

### ✔️ How EC2 uses this AMI

```hcl
ami = data.aws_ami.awslinux.id
```

No hardcoded AMI → safer & always updated.

### ✔️ Outputs

Terraform prints:

* AMI ID
* EC2 public IP

---

---

# 🟠 **EXAMPLE 2 — Create Two EC2 Instances Using count + Fetch One Using Data Source**

## ✅ **Full Code First**

### `main.tf`

```hcl
resource "aws_instance" "ins2" {
  ami           = "ami-0108d6a82a783b352"
  key_name      = var.key
  instance_type = var.instancetype
  count         = 2

  tags = {
    Name = "Count ${count.index}"
  }
}

data "aws_instance" "myawsinstance" {
  filter {
    name   = "tag:Name"
    values = ["Count 0"]
  }

  depends_on = [
    aws_instance.ins2
  ]
}
```

---

### `outputs.tf`

```hcl
output "fetched_instance_ip" {
  value = data.aws_instance.myawsinstance.public_ip
}
```

---

## 📝 **Explanation**

### ✔️ What the resource does

This code creates **two EC2 instances**:

| Instance   | Tag Name  |
| ---------- | --------- |
| Instance 0 | `Count 0` |
| Instance 1 | `Count 1` |

Thanks to:

```hcl
count = 2
```

### ✔️ What the data source does

`aws_instance` **reads** an existing EC2 instance (does NOT create):

```hcl
filter {
  name = "tag:Name"
  values = ["Count 0"]
}
```

Terraform now fetches:

* public_ip
* private_ip
* arn
* instance_id

### ✔️ depends_on

Ensures EC2s are created **before** Terraform tries to fetch them.

### ✔️ Output

Prints public IP of the instance with tag `"Count 0"`.

---

---

# 🔵 **EXAMPLE 3 — Fetch All EBS Volume IDs**

## ✅ **Full Code First**

### `provider.tf`

```hcl
provider "aws" {
  region = "ap-south-1"
}
```

---

### `main.tf`

```hcl
data "aws_ebs_volumes" "ebsvolume" {
  filter {
    name   = "volume-type"
    values = ["gp2"]
  }
}
```

---

### `outputs.tf`

```hcl
output "volume_ids" {
  value = data.aws_ebs_volumes.ebsvolume.ids
}
```

---

## 📝 **Explanation**

### ✔️ What this data source does

`aws_ebs_volumes` **reads all EBS volumes** in the region matching:

```
volume-type = gp2
```

Result returned:

* `ids` → list of all matching EBS volumes

### Example output:

```
volume_ids = [
  "vol-0a12345bcdef67890",
  "vol-0f98765abcd432100"
]
```

No resource is created — only information is fetched.

---

# 🎯 FINAL SUMMARY

| Example                           | What It Shows                               |
| --------------------------------- | ------------------------------------------- |
| **1. aws_ami**                    | Fetch latest Amazon Linux AMI → use in EC2  |
| **2. aws_instance (data source)** | Fetch an EC2 instance based on tag          |
| **3. aws_ebs_volumes**            | Fetch list of all EBS volumes using filters |

### ✔️ Data Sources → READ

### ✔️ Resources → CREATE

### ✔️ Filters help Terraform find EXACT resource you want.

---

