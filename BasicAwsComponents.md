## Manage S3 By Terraform

### Example1 - Create a private or public-read bucket
```terraform
provider "aws" {
profile="rprofile"
region="ap-south-1"
}
resource "aws_s3_bucket" "b" {
  bucket = "my-tf-test-bucket1238"
  acl    = "private"

  tags = {
    Name        = "My bucket"
    Environment = "Dev"
 }
}


```

### Example 2 - Add an object (index.html) in bucket
```terraform
provider "aws" {
profile="rprofile"
region="ap-south-1"
}
resource "aws_s3_bucket" "b" {
  bucket = "my-tf-test-bucket1238"
  acl    = "public-read"
  tags = {
    Name        = "My bucket"
    Environment = "Dev"
 }
}
resource "aws_s3_bucket_object" "newobj"{
  bucket = aws_s3_bucket.b.id
  key    = "profile"
  source="/home/ubuntu/index.html"
  etag=filemd5("/home/ubuntu/index.html")
}

```
### Example 2.1 - Add multiple files

```terraform
provider "aws" {
profile="rprofile"
region="ap-south-1"
}
resource "aws_kms_key" "examplekms" {
  description             = "KMS key 1"
  deletion_window_in_days = 7
}
resource "aws_s3_bucket" "b" {
  bucket = "my-tf-test-bucket1238"
  acl    = "public-read"
  tags = {
    Name        = "My bucket"
    Environment = "Dev"
 }
}
resource "aws_s3_bucket_object" "newobj"{
  bucket = aws_s3_bucket.b.id
  key    = "profile"
  source="/home/ubuntu/index.html"
  kms_key_id = aws_kms_key.examplekms.arn
}
resource "aws_s3_bucket_object" "object1" {
for_each = fileset("myfiles/", "*")
bucket = aws_s3_bucket.b.id
key = each.value
source = "myfiles/${each.value}"
etag = filemd5("myfiles/${each.value}")
}

```
## Example 3 Encrypting with KMS Key

```terraform
provider "aws" {
profile="rprofile"
region="ap-south-1"
}
resource "aws_kms_key" "examplekms" {
  description             = "KMS key 1"
  deletion_window_in_days = 7
}
resource "aws_s3_bucket" "b" {
  bucket = "my-tf-test-bucket1238"
  acl    = "public-read"
  tags = {
    Name        = "My bucket"
    Environment = "Dev"
 }
}
resource "aws_s3_bucket_object" "newobj"{
  bucket = aws_s3_bucket.b.id
  key    = "profile"
  source="/home/ubuntu/index.html"
  kms_key_id = aws_kms_key.examplekms.arn
}

```
