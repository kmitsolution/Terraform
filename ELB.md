## Create Security Group and EC2 instances
```terraform
#Create security group with firewall rules
resource "aws_security_group" "sg" {
  name        = "security_jenkins_port11"
  description = "security group for jenkins"

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

 ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

 # outbound from jenkis server
  egress {
    from_port   = 0
    to_port     = 65535
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags= {
    Name = "security_jenkins_port11"
  }
}

resource "aws_instance" "myFirstInstance" {
  ami           = "ami-04bde106886a53080"
  key_name = "devops"
  instance_type = "t2.micro"
  security_groups= [ aws_security_group.sg.name]
  count=2
  tags= {
    Name = "jenkins_instance"
  }
}


```
### Create ELB
```terraform
module "elb_http" {
  source  = "terraform-aws-modules/elb/aws"
  version = "~> 2.0"

  name = "elb-example1"

  subnets         = ["subnet-ddf575b4","subnet-90f2ede8"]
  security_groups = [aws_security_group.sg.id]
  internal        = false

  listener = [
    {
      instance_port     = 80
      instance_protocol = "HTTP"
      lb_port           = 80
      lb_protocol       = "HTTP"
    },
    {
      instance_port     = 8080
      instance_protocol = "http"
      lb_port           = 8080
      lb_protocol       = "http"

    },
  ]

  health_check = {
    target              = "HTTP:80/"
    interval            = 30
    healthy_threshold   = 2
    unhealthy_threshold = 2
    timeout             = 5
  }


  // ELB attachments
  number_of_instances = 2
  instances           = [aws_instance.myFirstInstance[0].id,aws_instance.myFirstInstance[1].id]

  tags = {
    Owner       = "user"
    Environment = "dev"
  }
}
```
