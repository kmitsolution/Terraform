## Terraform Blocks
This block can be called in 3 ways. All means the same. 
1. Terraform Block
2. Terraform Settings Block
3. Terraform Configuration Block

Each terraform block can contain a number of settings related to Terraform's behavior. 

### NOTE:-
Within a terraform block, only constant values can be used; arguments may not refer to named objects such as resources, input variables, etc, and may not use any of the Terraform language built-in functions.

<img width="523" alt="image" src="https://user-images.githubusercontent.com/84008107/142718184-0e8f6c17-94ee-4bd1-a7a9-e0c90449b68b.png">

# Terraform Settings, Providers & Resource Blocks
## Step-01: Introduction
1. <a href="https://www.terraform.io/docs/language/settings/index.html">Terraform Settings</a>
2. <a href="https://www.terraform.io/docs/providers/index.html">Terraform Providers</providers>
3. <a href="https://www.terraform.io/docs/language/resources/index.html"> Terraform Resources</a>
4. <a href="https://www.terraform.io/docs/language/functions/file.html">Terraform File Function</a>
5. Create EC2 Instance using Terraform and provision a webserver with userdata.

<img width="500" alt="image" src="https://user-images.githubusercontent.com/84008107/142721675-638b0d3d-03b5-4d11-9f89-f5757dfa4f7e.png">

## Step-02: In c1-versions.tf - Create Terraform Settings Block
Understand about Terraform Settings Block and create it

```terraform
# Terrform Block
terraform {
requrequired_version = "~> 1.0.8"  # allow right most version like 1.0.9 not the major version
                                   # if it is 1.0 then it will allow 1.xxxx because right most is 0 in this case
required_providers {
    //aws is an attribute you can specify any variable name here and same name is to mention on provider block
  aws ={   
      source="hashicorp/aws"
      version="~>3.0"
  }
}                                   
}
```
## Step-03: In c1-versions.tf - Create Terraform Providers Block
Understand about Terraform Providers

<img width="700" alt="image" src="https://user-images.githubusercontent.com/84008107/142721776-9019b7cb-7399-422e-9297-ac60c4f121fe.png">

<img width="700" alt="image" src="https://user-images.githubusercontent.com/84008107/142721812-40c8557b-2d85-4e42-8374-1a8e85aeb30c.png">

<img width="700" alt="image" src="https://user-images.githubusercontent.com/84008107/142721903-0f59c46c-8775-47d8-a238-623fd1f3f38b.png">

<img width="700" alt="image" src="https://user-images.githubusercontent.com/84008107/142721927-94382239-f39c-48de-b2be-07f205be3e68.png">

Configure AWS Credentials in the AWS CLI if not configured
# Verify AWS Credentials
1. cat $HOME/.aws/credentials
2. Create AWS Providers Block
# Provider Block
```terraform
# Terrform Block
terraform {
required_version = "~> 1.0.8"  # allow right most version like 1.0.9 not the major version
                                   # if it is 1.0 then it will allow 1.xxxx because right most is 0 in this case
required_providers {
    //aws is an attribute you can specify any variable name here and same name is to mention on provider block
  aws ={
      source="hashicorp/aws"
      version="~>3.0"
  }
}
}
provider "aws"{
region="ap-south-1"
profile="rprofile"
}
```

## Step-04: In c2-ec2instance.tf - Create Resource Block
Understand about Resources
1. Create EC2 Instance Resource
2. Understand about File Function
3. Understand about Resources - Argument Reference
4. Understand about Resources - Attribute Reference
### Resource: EC2 Instance

<img width="700" alt="image" src="https://user-images.githubusercontent.com/84008107/142721973-d3f57a19-8c2f-472f-b973-24917a7292ea.png">

```terraform
resource "aws_instance" "myins" {
    ami = "ami-0108d6a82a783b352"
    instance_type = "t2.micro"
    user_data = file("${path.module}/app1-install.sh")
    tags = {
      "Name" = "demo"
    }

}
```
## Step-05: Review file app1-install.sh
```linux
#! /bin/bash
# Instance Identity Metadata Reference - https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-identity-documents.html

sudo yum update -y
sudo yum install -y httpd
sudo systemctl enable httpd
sudo service httpd start  
sudo echo '<h1>Welcome to StackSimplify - APP-1</h1>' | sudo tee /var/www/html/index.html
sudo mkdir /var/www/html/app1
sudo echo '<!DOCTYPE html> <html> <body style="background-color:rgb(250, 210, 210);"> <h1>Welcome to Terraform - APP-1</h1> <p>Terraform Demo</p> <p>Application Version: V1</p> </body></html>' | sudo tee /var/www/html/app1/index.html
sudo curl http://169.254.169.254/latest/dynamic/instance-identity/document -o /var/www/html/app1/metadata.html
```
Step-06: Execute Terraform Commands
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

1) No changes - Just prints the execution plan

# Terraform Apply

terraform apply 

[or]

terraform apply -auto-approve

Observations:

1) Create resources on cloud

2) Created terraform.tfstate file when you run the terraform apply command

## Step-07: Access Application
Important Note: verify if default VPC security group has a rule to allow port 80
### Access index.html
curl localhost

curl localhost/app1/index.html

# Access metadata.html
curl localhost/app1/metadata.html

## Step-08: Terraform State - Basics
Understand about Terraform State

Terraform State file terraform.tfstate

<img width="700" alt="image" src="https://user-images.githubusercontent.com/84008107/142725555-e3e24d50-db1a-487a-89f6-e6807127f36a.png">

<img width="700" alt="image" src="https://user-images.githubusercontent.com/84008107/142725568-0d0cdf96-70a1-4e91-b730-fd2d7b6dae42.png">

Understand about Desired State and Current State

<img width="700" alt="image" src="https://user-images.githubusercontent.com/84008107/142725619-5dcef200-919f-42a9-ab4c-4e195294cf36.png">


## Step-09: Clean-Up
 
 Terraform Destroy

terraform plan -destroy  # You can view destroy plan using this command

terraform destroy

# Clean-Up Files

rm -rf .terraform*

rm -rf terraform.tfstate*



