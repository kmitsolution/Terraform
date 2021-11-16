## Terraform Provisioners 
        Provisioners are used to performing certain custom actions and tasks either on the local machine or on the remote machine.

### File Provisioner

### Example1 - Upload the file to an EC2 instance

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

### local-exec provisioner
This provisioner is used when you want to perform some tasks onto your local machine where you have installed the terraform.

So local-exec provisioner is never used to perform any kind task on the remote machine. It will always be used to perform local operations onto your local machine.

Example - Consider the following example where we are trying to create a file hello-world.txt on the local machine

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
