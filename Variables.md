Terraform supports different type of variables
<ul>
  <li> Strings </li>
  <li> Maps </li>
  <li> Lists </li>
  <li> Boolean </li>
</ul>

### Strings

#### Example - single line
```terraform
provider "aws"{
profile = "rprofile"
region = "ap-south-1"
}

variable "firststring" {
type = string
default = "this is my first string"
}

output "myfirstoutput" {
value = "${var.firststring}"
}

```

### Example - multiline

```terraform
provider "aws" {
profile = "rprofile"
region = "ap-south-1"
}
variable "multiline" {
type = string
default = <<EOT
            1
            2
            EOT

}

output "outputvar"{
 value = "${var.multiline}"
}

```
## Numbers

### Example

```terraform
provider "aws"{
profile = rprofile
region = "ap-south-1"
}

variable "pie"{
type = number
default = 3.14
}

output "output1"{
value = "${var.pie}"
}

```

## Bool

### Example

```terraform
provider "aws" {
profile="rprofile"
region="ap-south-1"
}
variable "IsiTTrue" {
type=bool
default=true
}
output "result" {
value= "${var.IsiTTrue}"
}


```

## Null
The null value is represented by the unquoted symbol null.

## Lists/Tuples
Lists/tuples are represented by a pair of square brackets containing a comma-separated sequence of values, like ["a", 15, true].

List literals can be split into multiple lines for readability, but always require a comma between values. 

### Example 1

```terraform
provider "aws" {
profile="rprofile"
region="ap-south-1"
}
variable "regions" {
type = list
default=["a","2","c"]

}

output "result" {
value="${var.regions}"
}

```
### Example 2
```terraform
provider "aws" {
profile="rprofile"
region="ap-south-1"
}
variable "regions" {
type = list
default=["a","2","c"]

}

output "result" {
value="${var.regions[0]}"
}

```

## Map/Objects
Maps are a collection of string keys and string values.
### Example

```terraform
provider "aws" {
profile="rprofile"
region="ap-south-1"
}
variable "regions" {
type = map
default={
   region1= "mumbai"
   number1=1

}

}

output "result" {
value="${var.regions}"
}

```
### Example2
```terraform
provider "aws" {
profile="rprofile"
region="ap-south-1"
}
variable "regions" {
type = map
default={
   region1= "mumbai"
   number1=1

}

}

output "result" {
value="${var.regions["region1"]}"
}

```

## Input Output variables

### Example :- input the value from user and display it

```terraform
provider "aws" {
profile="rprofile"
region="ap-south-1"
}
variable "myinput" {
type=string
}
output "result" {
value="${var.myinput}"
}

```

### Example

#### variables (variable.tf)
```terraform
variable "region" {
   description = "Mumbai region"
   type        = string
   default     = "ap-south-1"
}
variable "keyname" {
type=string
default="devops"
}
variable "instance_type" {
type=string
default="t2.micro"
}

variable "name" {
type=string
default="Test"
}


```

#### main.tf

```terraform
provider "aws"{
profile="rprofile"
region= var.region
}
resource "aws_instance" "myFirstInstance" {
  ami           = "ami-04bde106886a53080"
  key_name = var.keyname
  instance_type = var.instance_type
  tags= {
    Name = var.name
  }
}

```


### Example 2

#### variable.tf but without any default values
```terraform
variable "instance_type" {
}
```
#### create terraform.tfvars
instance_type="t2.micro"

#### main.tf
```terraform
resource "aws_instance" "myFirstInstance" {
  ami           = "ami-04bde106886a53080"
  key_name = "devops"
  instance_type = var.instance_type
  tags= {
    Name = "test"
  }
}

```

### Example 3 Terraform multiple tfvars file

#### variable.tf
```terraform
variable "instance_type" {
}

variable "environment_name" {
}

```

#### stag.tfvars
```terraform
instance_type="t2.micro"

environment_name ="stage"

```
#### main.tf
```terraform
provider "aws"{
profile="rprofile"
region= "ap-south-1"
}
resource "aws_instance" "myFirstInstance" {
  ami           = "ami-04bde106886a53080"
  key_name = "devops"
  instance_type = var.instance_type
  tags= {
    Name = var.environment_name
  }
}


```

### terraform apply -var-file="stag.tfvars"
