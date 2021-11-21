## Terraform For Loops, Lists, Maps and Count Meta-Argument
### Pre-requisite Note
We are using the default vpc in ap-south-1 region

### Step-01: Introduction
1. Terraform Meta-Argument: Count
2. Terraform Lists & Maps
3. List(string)
4. map(string)

<b>Terraform for loops</b>
1. for loop with List
2. for loop with Map
3. for loop with Map Advanced

<b>Splat Operators</b>
1. Legacy Splat Operator .*.
2. Generalized Splat Operator (latest)
3. Understand about Terraform Generic Splat Expression [*] when dealing with count Meta-Argument and multiple output values

### Step-02: base.tf
No changes

### Step-03: variables.tf - Lists and Maps
```terraform
# AWS EC2 Instance Type - List
variable "instance_type_list" {
  description = "EC2 Instnace Type"
  type = list(string)
  default = ["t3.micro", "t3.small"]
}


# AWS EC2 Instance Type - Map
variable "instance_type_map" {
  description = "EC2 Instnace Type"
  type = map(string)
  default = {
    "dev" = "t3.micro"
    "qa"  = "t3.small"
    "prod" = "t3.large"
  }
}

```
### Step-04: ec2securitygroups.tf and c4-ami-datasource.tf
No changes to both files

### Step-05: ec2instance.tf
```terraform
# EC2 Instance
resource "aws_instance" "myec2vm" {
  ami = data.aws_ami.amzlinux2.id 
  # How to reference List values ?
instance_type = var.instance_type_list[1]

# How to reference Map values ?
#instance_type = var.instance_type_map["prod"]

# Meta-Argument Count
count = 2

  key_name = var.instance_keypair
  vpc_security_group_ids = [aws_security_group.vpc-ssh.id, aws_security_group.vpc-web.id]  
  tags = {
    "Name" = "Count-Demo-${count.index}"
  }
}
```
### Step-06: outputs.tf
```terraform



# Output - For Loop with List
output "for_output_list" {
  description = "For Loop with List"
  value = [for instance in aws_instance.myec2vm: instance.public_dns ]
}

# Output - For Loop with Map
output "for_output_map1" {
  description = "For Loop with Map"
  value = {for instance in aws_instance.myec2vm: instance.id => instance.public_dns}
}

# Output - For Loop with Map Advanced
output "for_output_map2" {
  description = "For Loop with Map - Advanced"
  value = {for c, instance in aws_instance.myec2vm: c => instance.public_dns}
}

# Output Legacy Splat Operator (latest) - Returns the List
output "legacy_splat_instance_publicdns" {
  description = "Legacy Splat Expression"
  value = aws_instance.myec2vm.*.public_dns
}  

# Output Latest Generalized Splat Operator - Returns the List
output "latest_splat_instance_publicdns" {
  description = "Generalized Splat Expression"
  value = aws_instance.myec2vm[*].public_dns
}
```

### Step-07: Execute Terraform Commands
```
# Terraform Initialize
terraform init

# Terraform Validate
terraform validate

# Terraform Plan
terraform plan
Observations: 
1) play with Lists and Maps for instance_type

# Terraform Apply
terraform apply -auto-approve
Observations: 
1) Two EC2 Instances (Count = 2) of a Resource myec2vm will be created
2) Count.index will start from 0 and end with 1 for VM Names
3) Review outputs in detail (for loop with list, maps, maps advanced, splat legacy and splat latest)

```

### Step-09: Clean-Up

```
# Terraform Destroy
terraform destroy -auto-approve

# Files
rm -rf .terraform*
rm -rf terraform.tfstate*
```
