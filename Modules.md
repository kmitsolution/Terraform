## Terraform Modules
Module will help you to organize your terraform configuration so that you can re-use the configuration and keep your terraform code more clean and modular.


```terraform
provider "aws" {
profile="rprofile"
region="ap-south-1"
}
module "test" {
source=".//m1"
}
module "test1" {
source=".//m2"
}

```
