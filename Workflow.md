## Terraform Workflow

<img width="945" alt="image" src="https://user-images.githubusercontent.com/84008107/142707253-db2aa093-fe55-4537-b69d-92d1acf3cba1.png">

### Step-01: Introduction

#### Understand basic Terraform Commands

1. terraform init
2. terraform validate
3. terraform plan
4. terraform apply
5. terraform destroy
### Step-02: Review terraform manifest for EC2 Instance
1. <b>Pre-Conditions-1:</b> Ensure you have default-vpc in that respective region
2. <b>Pre-Conditions-2:</b> Ensure AMI you are provisioning exists in that region if not update AMI ID
3. <b>Pre-Conditions-3:</b> Verify your AWS Credentials in $HOME/.aws/credentials
### Terraform Settings Block
```terraform
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      #version = "~> 3.21" # Optional but recommended in production
    }
  }
}
```
### Provider Block
```terraform
provider "aws" {
  profile = "default" # AWS Credentials Profile configured on your local desktop terminal  $HOME/.aws/credentials
  region  = "us-east-1"
}
```

### Resource Block
```terrafrom
resource "aws_instance" "ec2demo" {
  ami           = "ami-04d29b6f966df1537" # Amazon Linux in us-east-1, update as per your region
  instance_type = "t2.micro"
}
```

## Step-03: Terraform Core Commands

### Initialize Terraform
terraform init

### Terraform Validate
terraform validate

### Terraform Plan to Verify what it is going to create / update / destroy
terraform plan

### Terraform Apply to Create EC2 Instance
terraform apply 

## Step-04: Verify the EC2 Instance in AWS Management Console
1. Go to AWS Management Console -> Services -> EC2
2. Verify newly created EC2 instance

## Step-05: Destroy Infrastructure
### Destroy EC2 Instance
terraform destroy

### Delete Terraform files 

1. rm -rf .terraform*
2. rm -rf terraform.tfstate*

## Step-08: Conclusion

Learned about Important Terraform workflow Commands
1. terraform init
2. terraform validate
3. terraform plan
4. terraform apply
5. terraform destroy
