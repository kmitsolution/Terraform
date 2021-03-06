### Install awscli for Linux
1. curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
2. unzip awscliv2.zip
3. sudo ./aws/install
4. aws --version

### Aws Configuration (rprofile is the name of the profile)
1. aws configure --profile rprofile
2. aws iam list-users --profile rprofile


### Install Terraform on ubuntu
1. curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
2. sudo apt-add-repository "deb [arch=$(dpkg --print-architecture)] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
3. sudo apt install terraform


Terraform Basics
1. First Terraform file
2. What is statefile and its importance
3. Different ways to give access to tf
4. Terraform Variables and its usages
5. Terraform input variables
6. Terraform output variables

### terraform init
### terraform plan
### terraform apply
### terraform destroy

# Terraform State file
 .tfstate
 
### Example1 - Create an empty terraform file(.tf)

<ul>
   <li> Create an empty directory called( say 01)</l1>
   <li> Goto 01 directory (cd 01) </li>
   <li> Create an empty file called hello.tf </li>
   <li> run command <b>terraform init </b></li>
   <li> run command <b>terraform plan </b></li>
   <li> run command <b>terraform apply</b> it should create <b>terraform.tfstate</b> file </li>
</ul>

### Example2 - Create a provider aws
<ul>
  <li> Add following code  <br />
      provider "aws"{<br />

                 }
   </li>	
 <li> run command <b>terraform init</b> command   </li>
 <li> it will create .terraform directory for aws plugins </li>
</ul>

## Authentication
The AWS provider offers a flexible means of providing credentials for authentication. The following methods are supported, in this order, and explained below:

1. Static credentials
2. Environment variables
3. Shared credentials/configuration file
4. CodeBuild, ECS, and EKS Roles
5. EC2 Instance Metadata Service (IMDS and IMDSv2)

### Static credentials
Static credentials can be provided by adding an access_key and secret_key in-line in the AWS provider block:

<b>Usage:</b>

```terraform
provider "aws" {
  region     = "us-west-2"
  access_key = "my-access-key"
  secret_key = "my-secret-key"
}
```
#### Example (To create an EC2 instance with a key)
```terraform
provider "aws" {
  region     = "ap-south-1"
  access_key = "AKIAZYCCNWPBYU7NQN7M"
  secret_key = "BeO52msn/TiT3p1YrE1xkpnMsHokSEzLiCR0ZL/u"
}
resource "aws_instance" "example" {
  ami           = "ami-041db4a969fe3eb68"
  instance_type = "t2.micro"
  key_name = "devops"
  tags = {
    Name = "HelloWorld"
  }
}

```

Environm
