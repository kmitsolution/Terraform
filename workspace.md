<b>Terraform worksapces</b> is a very logical concept where you can have multiple states of your infrastructure configuration. To put this in simple words if you are running an infrastructure configuration in development environment then the same infrastructure can be run in the production environment.

The main benefit of terraforming workspaces we get is we can have more than one state associated with a single terraform configuration.

1. terraform workspace list
2. terraform workspace select <<workspace name>>
3. terraform workspace new <<workspace name>>
  
```terraform
  
provider "aws" {
profile="rprofile"
region="ap-south-1"
}

locals {
  instance_name = "${terraform.workspace}-instance"
}

resource "aws_instance" "ec2_example" {

    ami = "ami-04bde106886a53080"

    instance_type = "t2.micro"

    tags = {
      Name = local.instance_name
    }
}
```
