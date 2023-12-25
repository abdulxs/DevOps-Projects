Automate AWS Infrastructure with Terraform


Set up key-pair on AWS before proceeding so that you have access keys
Download the .pem file and store to your project directory

Set up VPC

create a terraform file in your project directory, let's name it main.tf
reference the Terraform documentation for aws_vpc resource
go to ```https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/vpc``` in your browser

copy the basic usage and paste in your main.tf file

```
resource "aws_vpc" "prod-vpc" {
  cidr_block = "10.0.0.0/16"
  tags = {
    Name = "production"
  }
}
```

Create an internet gateway
Repeat same step as above by referencing the terraform documentation for creating aws_internet_gateway resource
copy the basic usage, paste in your ```main.tf``` file and modify it to reference your vpc resource as below 

```
resource "aws_internet_gateway" "gw" {
  vpc_id = aws_vpc.prod-vpc.id

}
```

Create a custom route table

search for 'aws_route_table' resource in the Terraform docs
copy the usage and paste to your main.tf file

again, modify it to reference your vpc and internet gateway. Set the route to default

```
resource "aws_route_table" "prod-route-table" {
  vpc_id = aws_vpc.prod-vpc.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.gw.id
  }

  route {
    ipv6_cidr_block        = "::/0"
    gateway_id = aws_internet_gateway.gw.id
  }

  tags = {
    Name = "Prod"
  }
}
```

Create a subnet

```
resource "aws_subnet" "subnet-1" {
  vpc_id     = aws_vpc.prod-vpc.id
  cidr_block = "10.0.1.0/24"
  availability_zone = "eu-north-1a"

  tags = {
    Name = "prod-subnet"
  }
}
```

Associate subnet with route table

```
resource "aws_route_table_association" "a" {
  subnet_id      = aws_subnet.subnet-1.id
  route_table_id = aws_route_table.prod-route-table.id
}
```

Create security group

We will reference our vpc_id, and
set ingress rules for allowing traffic from and to all IP addresses over https, http and ssh

```
resource "aws_security_group" "allow_web" {
  name        = "allow_web_traffic"
  description = "Allow web inbound traffic"
  vpc_id      = aws_vpc.prod-vpc.id

  ingress {
    description      = "HTTPS"
    from_port        = 443
    to_port          = 443
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = [aws_vpc.main.ipv6_cidr_block]
  }

  ingress {
    description      = "HTTP"
    from_port        = 80
    to_port          = 80
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = [aws_vpc.main.ipv6_cidr_block]
  }

  ingress {
    description      = "SSH"
    from_port        = 22
    to_port          = 22
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = [aws_vpc.main.ipv6_cidr_block]
  }

  egress {
    from_port        = 0
    to_port          = 0
    protocol         = "-1"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = ["::/0"]
  }

  tags = {
    Name = "allow_web"
  }
}
```
Create a network interface with an Ip in the subnet created in previous steps

reference the subnet
set the IP of the network interface and reference the security group as well

```
#create a network interface
resource "aws_network_interface" "web-server-nic" {
  subnet_id       = aws_subnet.subnet-1.id
  private_ips     = ["10.0.1.50"]
  security_groups = [aws_security_group.allow_web.id]
}
```

Assign an elastic ip to the network interface created in the last step

```
resource "aws_eip" "one" {
  domain                    = "vpc"
  network_interface         = aws_network_interface.web-server-nic.id
  associate_with_private_ip = "10.0.1.50"
  depends_on = [ aws_internet_gateway.gw ]
}
```
Create Ubuntu server and install/enable apache

ensure to specify the availablility zone to the same as specified for the subnet
Ensure to add your key name that was downloaded at the start
Specify the network interface

```
resource "aws_instance" "web-server-instance" {
  ami           = "ami-0014ce3e52359afbd"
  instance_type = "t3.micro"
  availability_zone = "eu-north-1a"
  
  key_name = "lagbaja"
  network_interface {
    device_index = 0
    network_interface_id = aws_network_interface.web-server-nic.id
  }

  user_data = <<-EOF
                #!/bin/bash
                sudo apt update -y
                sudo apt install apache2 -y
                sudo systemctl start apache2
                sudo bash -c 'echo our web server > /var/www/html/index.html'
                EOF

  tags = {
    Name = "Web-server"
  }
}
```


Worth noting that on deployment, the instance is initialized and everything seems to work fine, except when I try to access the web server in my browser, I get this error
<img width="999" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/b6c03a66-1d29-4e7f-a26e-76b19df6c9da">

<img width="1187" alt="image" src="https://github.com/abdulxs/DevOps-Projects/assets/18741380/271d972e-ee7b-43d8-86aa-489c01f4a6d0">


I ssh into the instance and I checked the logs with this command 
```
sudo -i

cat /var/log/cloud-init-log
``` 
tI observed some errors with a snippet below, it appears there is a connection issue preventing the script in the user data from running successfully. Due to this, it is unable to connect to the blahblah address and by extension install apache on this instance
This is also why we are unable to connect to the server via port 80 or 443

```
Cannot initiate the connection to security.ubuntu.com:80 (2620:2d:4002:1::103). - connect (101: Network is unreachable) Could not connect to security.ubuntu.com:80 (185.125.190.36), connection timed out Could not connect to security.ubuntu.com:80 (91.189.91.81), connection timed out Could not connect to security.ubuntu.com:80 (185.125.190.39), connection timed out Could not connect to security.ubuntu.com:80 (91.189.91.82), connection timed out Could not connect to security.ubuntu.com:80 (91.189.91.83), connection timed out
```

Troubleshooting Notes

This has taken me on a deep dive of network troubleshooting
I have tested the dns resolution of the instance, apparently, when I ping ```security.ubuntu.com:80``` it comes back as successful and when I run ```sudo apt update -y``` manually on the instance, it is also successful.
However, there appears to be an underlying network issue preventing the commands from being successful when run automatically
A review of the network configuration is still in progress




