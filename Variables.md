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
