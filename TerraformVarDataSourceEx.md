## Terraform Variables and Datasources
Pre-requisite 

1. Create a key(devops in this example I have taken) in AWS EC2 Key pairs which we will reference in our EC2 Instance.
2. Get latest EC2 AMI ID Using Terraform Datasources concept
3. We are also going to use existing EC2 Key pair terraform-key
4. Use all the above to create an EC2 Instance in default VPC

### Step-01: Example Topics to cover
1. Terraform Concepts
2. Terraform Input Variables
3. Terraform Datasources
4. Terraform Output Values

#### Below Input variables will be created
1. AWS Region
2. Instance Type
3. Key Name

#### Define Security Groups and Associate them as a List item to AWS EC2 Instance
1. vpc-ssh
2. vpc-web

#### Learn about Terraform Output Values
1. Public IP
2. Public DNS


### Step-02:- Creating Variables
1. <a href="https://www.terraform.io/docs/language/values/variables.html"> Input Variables </a>
##### variables.tf

```terraform
# AWS Region
variable "aws_region" {
  description = "Region in which AWS Resources to be created"
  type = string
  default = "ap-south-1"  
}

# AWS EC2 Instance Type
variable "instance_type" {
  description = "EC2 Instance Type"
  type = string
  default = "t3.micro"  
}

# AWS EC2 Instance Key Pair
variable "instance_keypair" {
  description = "AWS EC2 Key pair that need to be associated with EC2 Instance"
  type = string
  default = "devops"
}
# AWS Security Group
variable "sg" {
    description = "AWS Security Groups"
    type=list
    default = ["vpc-ssh","vpc-web"]
  
}
```
Reference the variables in base.tf fies
#### base.tf file
```terraform
provider "aws"{
region=var.aws_region
profile="rprofile"
}

```

### Step-03: ec2securitygroups.tf - Define Security Group Resources in Terraform
<a href="https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/security_group"> Security Group </a>

```terraform
# Create Security Group - SSH Traffic
resource "aws_security_group" "vpc-ssh" {
  name        = var.sg[0]
  description = "Dev VPC SSH"
  ingress {
    description = "Allow Port 22"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  egress {
    description = "Allow all ip and ports outboun"
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

# Create Security Group - Web Traffic
resource "aws_security_group" "vpc-web" {
  name        = var.sg[1]
  description = "Dev VPC web"
  ingress {
    description = "Allow Port 80"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    description = "Allow Port 443"
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    description = "Allow all ip and ports outbound"
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

```

### Step-04: ami-datasource.tf - Define Get Latest AMI ID for Amazon Linux2 OS
<a href="https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/ami" > Data Source:-Aws Ami </a>

```terrafrom
# Get latest AMI ID for Amazon Linux2 OS
# Get Latest AWS AMI ID for Amazon2 Linux
data "aws_ami" "amzlinux2" {
  most_recent = true
  owners = [ "amazon" ]
  filter {
    name = "name"
    values = [ "amzn2-ami-hvm-*-gp2" ]
  }
  filter {
    name = "root-device-type"
    values = [ "ebs" ]
  }
  filter {
    name = "virtualization-type"
    values = [ "hvm" ]
  }
  filter {
    name = "architecture"
    values = [ "x86_64" ]
  }
}

```
### Step-05: ec2instance.tf - Define EC2 Instance Resource

<a href="https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/instance"> Ec2 Instance </a>

```terraform
# EC2 Instance
resource "aws_instance" "myec2vm" {
  ami = data.aws_ami.amzlinux2.id 
  instance_type = var.instance_type
  key_name = var.instance_keypair
  vpc_security_group_ids = [aws_security_group.vpc-ssh.id, aws_security_group.vpc-web.id]  
  tags = {
    "Name" = "EC2 Demo "
  }
}

```

### Step-06: outputs.tf - Define Output Values

<a href="https://www.terraform.io/docs/language/values/outputs.html"> Output Variables </a>

```terraform
# Terraform Output Values
output "instance_publicip" {
  description = "EC2 Instance Public IP"
  value = aws_instance.myec2vm.public_ip
}

output "instance_publicdns" {
  description = "EC2 Instance Public DNS"
  value = aws_instance.myec2vm.public_dns
}

```

### Step-07: Execute Terraform Commands

```
# Terraform Initialize
terraform init
Observation:
1) Initialized Local Backend
2) Downloaded the provider plugins (initialized plugins)
3) Review the folder structure ".terraform folder"

# Terraform Validate
terraform validate
Observation:
1) If any changes to files, those will come as printed in stdout (those file names will be printed in CLI)

# Terraform Plan
terraform plan
Observation:
1) Verify the latest AMI ID picked and displayed in plan
2) Verify the number of resources that going to get created
3) Verify the variable replacements worked as expected

# Terraform Apply
terraform apply 
[or]
terraform apply -auto-approve
Observations:
1) Create resources on cloud
2) Created terraform.tfstate file when you run the terraform apply command
3) Verify the EC2 Instance AMI ID which got created

```

### Step-08: Clean-Up
```
# Terraform Destroy
terraform plan -destroy  # You can view destroy plan using this command
terraform destroy

# Clean-Up Files
rm -rf .terraform*
rm -rf terraform.tfstate*

```
