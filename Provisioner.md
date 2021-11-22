## Terraform Provisioners 
Provisioners are used to performing certain custom actions and tasks either on the local machine or on the remote machine.

### File Provisioner

### Example1(Ubuntu) - Upload the file to an EC2 instance

```terraform
provider "aws" {
profile="rprofile"
region="ap-south-1"
}
resource "aws_instance" "ec2_example" {

    ami = "ami-04bde106886a53080"
    instance_type = "t2.micro"
    key_name= "aws_key"
    vpc_security_group_ids = [aws_security_group.main.id]

  provisioner "file" {
    source      = "/home/ubuntu/1.txt"
    destination = "/home/ubuntu/test-file.txt"
  }
  connection {
      type        = "ssh"
      host        = self.public_ip
      user        = "ubuntu"
      private_key = file("/home/ubuntu/aws_key")
      timeout     = "4m"
   }
}

resource "aws_security_group" "main" {
  egress = [
    {
      cidr_blocks      = [ "0.0.0.0/0", ]
      description      = ""
      from_port        = 0
      ipv6_cidr_blocks = []
      prefix_list_ids  = []
      protocol         = "-1"
      security_groups  = []
      self             = false
      to_port          = 0
    }
  ]
 ingress                = [
   {
     cidr_blocks      = [ "0.0.0.0/0", ]
     description      = ""
     from_port        = 22
     ipv6_cidr_blocks = []
     prefix_list_ids  = []
     protocol         = "tcp"
     security_groups  = []
     self             = false
     to_port          = 22
  }
  ]
}

resource "aws_key_pair" "deployer" {
  key_name   = "aws_key"
  public_key = "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDYQIjBKUkXyvWRrrDvLyc5hqs+8CFnCoS8LOR847TJ9NwMVPT5KsKg17k9a/edvemMCZtB15fZS2YsK/EsawacGSlaruhr5mCGQ9p8GVzkGCkjTx8gRGKU23D7KCi6lIBPMO47FbYmb22OemEinwox4vpw41V6AeRDdxnVGiw6xOKruAcN05XhUsTYw3GYoRACWn78fGsZL8YrWyK0Xze0tZlOX/48x9wD/co1znw/JkfUEjhfeCvYtnWYESy521dG9388S/8vhOXfSY4eD57PGvJj5eaN1BfizfzkQn6IviOx/lEtBGL1bIrn3nBaJROzW8nUMgUrd0APimQTERGF root@terraform"
}

```
### Example1(Windows) - Upload the file to an EC2 instance
```terraform
resource "aws_instance" "ec2_example" {

    ami = "ami-04bde106886a53080"
    instance_type = "t2.micro"
    key_name= "aws_key"
    vpc_security_group_ids = [aws_security_group.main.id]

  provisioner "file" {
    source      = "C:\\TerraformScript\\02\\app1-install.sh"
    destination = "/home/ubuntu/test-file.txt"
  }
  connection {
      type        = "ssh"
      host        = self.public_ip
      user        = "ubuntu"
      private_key = file("C:\\TerraformScript\\02\\aws_key")
      timeout     = "4m"
   }
}

resource "aws_security_group" "main" {
  egress = [
    {
      cidr_blocks      = [ "0.0.0.0/0", ]
      description      = ""
      from_port        = 0
      ipv6_cidr_blocks = []
      prefix_list_ids  = []
      protocol         = "-1"
      security_groups  = []
      self             = false
      to_port          = 0
    }
  ]
 ingress                = [
   {
     cidr_blocks      = [ "0.0.0.0/0", ]
     description      = ""
     from_port        = 22
     ipv6_cidr_blocks = []
     prefix_list_ids  = []
     protocol         = "tcp"
     security_groups  = []
     self             = false
     to_port          = 22
  }
  ]
}

resource "aws_key_pair" "deployer" {
  key_name   = "aws_key"
  public_key = "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCjqMDGD5UuqfZh++TT/ds3jR3G1KynzTEHk/Vi+N/Q/OBztwGuTYDpmZYU5w+Izt4+IbDcYH+cYvANS1ASBrjj+gj4ZXgYa47tBLAFiyJCmgOl0bGku8Yjd0NCvm7rSkMPeBLVNkj2B9yC6exV91lwdoPZVnANmE8CmQ3w2fMa4R4lkkmRhXnJOvbm0LDDQVP49GB077rWLW+BrXd1utArfxvQSV976uO4IvAmjnu6DJsn8UCfT+9Ma5a19WMh1fKukx8mQCtRK55v3BJD+GeXxGpwibsDaCm7yS+RriTx0X/hPqRjOOYeJh4SmNrmK1XchMw8cAF3g2nYURZ2YmqxPomTknb6JlJNzzFmvTdIADqpJpEzMwn/olF/z/bOy5mQXL9tekAdbtMqcEeiLOns7HsYZIeF9HBW9UJtc0LAfkT+NEfTKXPN7r/QZvZh30tUR1r3ICxnf6dE6V/PdON8IfSddRdCPhfoSfGs8BDNwb97z6E+BircGJ+ikDaGGBc= raman@Raman-Sharma"
}
```
### local-exec provisioner
This provisioner is used when you want to perform some tasks onto your local machine where you have installed the terraform.

So local-exec provisioner is never used to perform any kind task on the remote machine. It will always be used to perform local operations onto your local machine.

## Example(Ubuntu) - Consider the following example where we are trying to create a file hello-world.txt on the local machine

```terraform
provider "aws" {
profile="rprofile"
region="ap-south-1"
}
resource "aws_instance" "ec2_example" {

    ami = "ami-04bde106886a53080"
    instance_type = "t2.micro"
    key_name= "devops"

  provisioner "local-exec" {
    command ="touch hello-world.txt"
  }
}



```

### Example (Windows)
```terraform
resource "aws_instance" "ec2_example" {

    ami = "ami-04bde106886a53080"
    instance_type = "t2.micro"
    key_name= "devops"

  provisioner "local-exec" {
    command ="echo 'Test' >> hello-world.txt"
  }
}
```

### remote-exec provisioner
As the name suggests remote-exec it is always going to work on the remote machine. With the help of the remote-exec you can specify the commands of shell scripts that want to execute on the remote machine.

```terraform
provider "aws" {
profile="rprofile"
region="ap-south-1"
}
resource "aws_instance" "ec2_example" {

    ami = "ami-04bde106886a53080"
    instance_type = "t2.micro"
    key_name= "aws_key"
    vpc_security_group_ids = [aws_security_group.main.id]

  provisioner "remote-exec" {
    inline = [
      "touch hello.txt",
      "echo helloworld remote provisioner >> hello.txt",
    ]
  }
  connection {
      type        = "ssh"
      host        = self.public_ip
      user        = "ubuntu"
      private_key = file("/home/ubuntu/aws_key")
      timeout     = "4m"
   }
}

resource "aws_security_group" "main" {
  egress = [
    {
      cidr_blocks      = [ "0.0.0.0/0", ]
      description      = ""
      from_port        = 0
      ipv6_cidr_blocks = []
      prefix_list_ids  = []
      protocol         = "-1"
      security_groups  = []
      self             = false
      to_port          = 0
    }
  ]
 ingress                = [
   {
     cidr_blocks      = [ "0.0.0.0/0", ]
     description      = ""
     from_port        = 22
     ipv6_cidr_blocks = []
     prefix_list_ids  = []
     protocol         = "tcp"
     security_groups  = []
     self             = false
     to_port          = 22
  }
  ]
}

resource "aws_key_pair" "deployer" {
  key_name   = "aws_key"
  public_key = "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDYQIjBKUkXyvWRrrDvLyc5hqs+8CFnCoS8LOR847TJ9NwMVPT5KsKg17k9a/edvemMCZtB15fZS2YsK/EsawacGSlaruhr5mCGQ9p8GVzkGCkjTx8gRGKU23D7KCi6lIBPMO47FbYmb22OemEinwox4vpw41V6AeRDdxnVGiw6xOKruAcN05XhUsTYw3GYoRACWn78fGsZL8YrWyK0Xze0tZlOX/48x9wD/co1znw/JkfUEjhfeCvYtnWYESy521dG9388S/8vhOXfSY4eD57PGvJj5eaN1BfizfzkQn6IviOx/lEtBGL1bIrn3nBaJROzW8nUMgUrd0APimQTERGF root@terraform"
}

```
