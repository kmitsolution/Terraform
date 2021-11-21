## Terraform for_each Meta-Argument with Functions toset, tomap

### Step-01: Introduction
1. for_each Meta-Argument
2. toset function
3. tomap function
4. Data Source: aws_availability_zones

### Step-02: No changes to files
1. versions.tf
2. variables.tf
3. ec2securitygroups.tf
4. ami-datasource.tf

### Step-03: ec2instance.tf
To understand more about <a href="https://www.terraform.io/docs/language/meta-arguments/for_each.html">for each </a>

<a href="https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/availability_zones">Aws Availability Zone </a>

```terraform
# Availability Zones Datasource
data "aws_availability_zones" "my_azones" {
  filter {
    name   = "opt-in-status"
    values = ["opt-in-not-required"]
  }
}
# EC2 Instance
resource "aws_instance" "myec2vm" {
  ami = data.aws_ami.amzlinux2.id 
  # How to reference List values ?
instance_type = var.instance_type_list[1]

# How to reference Map values ?
#instance_type = var.instance_type_map["prod"]

# Meta-Argument Count


  key_name = var.instance_keypair
  vpc_security_group_ids = [aws_security_group.vpc-ssh.id, aws_security_group.vpc-web.id]  
 # Create EC2 Instance in all Availabilty Zones of a VPC  
  for_each = toset(data.aws_availability_zones.my_azones.names)
  availability_zone = each.key # You can also use each.value because for list items each.key == each.value
  tags = {
    "Name" = "For-Each-Demo-${each.key}"
  }
}

```
### Step-04 output.tf

```terraform

# EC2 Instance Public IP with TOSET
output "instance_publicip" {
  description = "EC2 Instance Public IP"
  #value = aws_instance.myec2vm.*.public_ip   # Legacy Splat
  #value = aws_instance.myec2vm[*].public_ip  # Latest Splat
  value = toset([
      for myec2vm in aws_instance.myec2vm : myec2vm.public_ip
    ])  
}

# EC2 Instance Public DNS with TOSET
output "instance_publicdns" {
  description = "EC2 Instance Public DNS"
  #value = aws_instance.myec2vm[*].public_dns  # Legacy Splat
  #value = aws_instance.myec2vm[*].public_dns  # Latest Splat
  value = toset([
      for myec2vm in aws_instance.myec2vm : myec2vm.public_dns
    ])    
}

# EC2 Instance Public DNS with MAPS
output "instance_publicdns2" {
  value = tomap({
    for s, myec2vm in aws_instance.myec2vm : s => myec2vm.public_dns
    # S intends to be a subnet ID
  })
}

```

### Step-05: Execute Terraform Commands
```terraform
# Terraform Initialize
terraform init

# Terraform Validate
terraform validate

# Terraform Plan
terraform plan

# Terraform Apply
terraform apply -auto-approve
```
### Step-07: Clean-Up
```
# Terraform Destroy
terraform destroy -auto-approve

# Clean-Up
rm -rf .terraform*
rm -rf terraform.tfstate*
```
