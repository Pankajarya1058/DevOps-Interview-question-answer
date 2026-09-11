
> Note: The Terraform State tells Terraform what the current known state of the infrastructure is according to Terraform and which resources Terraform is managing.

## What is Terraform state?
- Suppose Terraform created EC2 in AWS.
  ```
  resource "aws_instance" "web" {
    ami           = "ami-xxxx"
    instance_type = "t3.micro"
  }
  ```
- Now, you change the codes after some days.
  ```
  resource "aws_instance" "web" {
    ami           = "ami-xxxx"
    instance_type = "t3.small"
  }
  ```
- Terraform needs to understand: "What was the first EC2 created?"
- And "What is its current non-declarative information?"
- This is where state comes into play.

## Main purpose of State
