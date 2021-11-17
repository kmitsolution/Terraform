## Create Group and Users

```terraform
provider "aws" {
   region     = "ap-south-1"
   profile="rprofile"
}
resource "aws_iam_user_group_membership" "example1" {
  user = aws_iam_user.user1.name

  groups = [
    aws_iam_group.group1.name,
    aws_iam_group.group2.name,
  ]
}

resource "aws_iam_user_group_membership" "example2" {
  user = aws_iam_user.user1.name

  groups = [
    aws_iam_group.group3.name,
  ]
}

resource "aws_iam_user" "user1" {
  name = "users1"
}

resource "aws_iam_group" "group1" {
  name = "group1"
}

resource "aws_iam_group" "group2" {
  name = "group2"
}

resource "aws_iam_group" "group3" {
  name = "group3"
}

```
