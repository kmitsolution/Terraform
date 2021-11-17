## Understanding terraform count, for_each and for loop
When you are working with collection variables inside Terrafrom then you must understand the concept of loops with count, loops with for each and `for loop, otherwise, it will be really hard to iterate over collections such list, map and set.

### Exmaple creating users using count
```terraform
provider "aws" {
   region     = "ap-south-1"
   profile="rprofile"
}

resource "aws_iam_user" "example" {
  count = length(var.user_names)
  name  = var.user_names[count.index]
}

variable "user_names" {
  description = "IAM usernames"
  type        = list(string)
  default     = ["users1", "users2", "users3"]
}

```

### Example Loops with for_each
```terraform
provider "aws" {
   region     = "ap-south-1"
   profile="rprofile"
}

resource "aws_iam_user" "example" {
  for_each = var.user_names
  name  = each.value
}

variable "user_names" {
  description = "IAM usernames"
  type        = set(string)
  default     = ["users1", "users2", "users3"]
}

```
