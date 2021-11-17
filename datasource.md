## Terraform Data Sources
Terraform Data Sources are a kind of an API that fetches the data/information from the resources running under the cloud infra and sending it back to terraform configuration for further use.

<b>filter:</b> Although we have created only one instance but still we have used filter because in a production-like environment you might have multiple aws_instance running, so you need to filter the instance anyhow. And since we have tagged our aws_instance with the name Terraform EC2 so we are going to use the same name inside the filter also.

<b>depends_on:</b> The second important parameter is depends_on because data source does not know by its own which resource it belongs to, so we are going to add the depends_on parameter.

### Example to find the public ip of created Instance
```terraform
provider "aws" {
    region     = "ap-south-1"
    profile = "rprofile"
}

resource "aws_instance" "ec2_example" {

    ami           = "ami-04bde106886a53080"
    instance_type =  "t2.micro"

    tags = {
      Name = "Terraform EC2"
    }
}

data "aws_instance" "myawsinstance" {
    filter {
        name = "tag:Name"
        values = ["Terraform EC2"]
    }

    depends_on = [
      "aws_instance.ec2_example"
    ]
}

output "fetched_info_from_aws" {
  value = data.aws_instance.myawsinstance.public_ip
}

```

### Example to list all Ebs volume

```terraform
provider "aws" {
   region     = "ap-south-1"
   profile="rprofile"
}

data "aws_ebs_volumes" "ebsvolume" {

  filter {
    name = "volume-type"
    values = [
      "gp2"]
  }
}
output "result" {
value=data.aws_ebs_volumes.ebsvolume.ids
}

``` 
