Automate AWS Infrastructure with Terraform
Set up key-pair on AWS before proceeding so that you have access keys
Download the .pem file and store to your project directory

Set up VPC

create a terraform file in your project directory, let's name it main.tf
reference the Terraform documentation for aws_vpc resource
go to ```https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/vpc``` in your browser

copy the basic usage

```
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}
```

Create internet gateway
