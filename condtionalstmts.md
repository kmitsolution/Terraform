## Exmaple :- Create a vpc if the env is prod

```terraform
provider "aws"{
profile = "rprofile"
region = "ap-south-1"
}
variable "env" {
  default="stag"
}

resource "aws_vpc" "my_vpc" {
count = "${var.env=="prod" ? 1 :0}"
cidr_block ="10.2.0.0/16"

}

```
