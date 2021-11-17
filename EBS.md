## Create EBS Volume
```terraform
provider "aws" {
   region     = "ap-south-1"
   profile="rprofile"
}
resource "aws_ebs_volume" "ebsvolume" {
  availability_zone = "ap-south-1a"
  size = 10
  encrypted = false
  tags = {
    name = "raman test vol"
  }

}

```

## Attach the volume
```terraform
provider "aws" {
   region     = "ap-south-1"
   profile="rprofile"
}
resource "aws_volume_attachment" "mountvolumetoec2" {
  device_name = "/dev/sdb"
  instance_id = "i-097aa2bd6661230a7"
  volume_id = "vol-068c15fe332d5e463"
}

```
