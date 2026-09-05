## Variables
- A variable is an input value that makes the Terraform configuration dynamic and reusable.
- Example:
  ```bash
  # Variable define
  variable "instance_type" {
    type    = string
    default = "t3.micro"
  }
  ```
  ```bash
  # resource
  resource "aws_instance" "web" {
    instance_type = var.instance_type
  }
  ```
## How to access variable
  - Syntax: ```var.variable_name```
  - If Variable is defined.
    ```bash
    variable "region" {
      default="ap-south-1"
    }
    ```
  - To use:
    ```bash
    provider "aws" {
      region = var.region
    }
    ```
## Variable Types
- String
  ```bash
  variable "environment" {
    type = string
    default = "Dev"
  }
  ```
- Number
  ```bash
  variable "instance_count" {
    type = number
    default = 2
  }
  ```
- Boolean
  ```bash
  variable "enable_monitoring" {
    type = bool
    default = true
  }
  ```
- list
  ```bash
  variable "availability_zones" {
    type = list(string)

    default = [
      "ap-south-1"
      "ap-south-1b"
    ]
  }
  ```
- set
- map
- object
- tuple

## There are multiple ways to provide variable values ​​in Terraform.
- Important ones:
  ```
  1. default
  2. terraform.tfvars
  3. *.auto.tfvars
  4. -var
  5. -var-file
  6. Environment variables
  ```
- Example:
  ```
  terraform apply -var="instance_type=t3.small"
  ```

### terraform.tfvars
- This is commonly used to store actual value of variables.
- Terraform automatically loads terraform.tfvars
  ```bash
  # variables.tf
  
  variable "region" {
    type = string
  }

  variable "instance_type" {
    type = string
  }
  ```
  ```bash
  # terraform.tfvars
  
  region = "ap-south-1"
  instance_types = "t3.micro"
  ```
  ```bash
  # Resource

  provider "aws" {
    region = var.region
  }

  resource "aws_instance" "web" {
    instance_type = var.instance_type
  }
  ```

### variables.tf vs terraform.tfvars
- **variables.tf**
  - Used to declare/define variable.
    ```bash
    variable "instance_type" {
      type = string
    }
    ```
- **terraform.tfvars**
  - It provides value of variable.
    ```bash
    instance_type = "t3.micro"
    ```
> variables.tf = What input do I need?
>
> terraform.tfvars = What value should that input have?

### ⚠️ terraform.tfvars vs .tf
- Terraform automatically loads ```terraform.tfvars``` and ```*.auto.tfvars``` for variable values.
- But, Terraform does not loads ```dev.tfvars``` or ```prod.tfvars``` automatically.
- Terraform use: ```terraform apply -var-file="dev.tfvars"``` or ```terraform apply -var-file="prod.tfvars"``` to loads dev.tfvars or prod.tfvars.

## Outputs
- Output extracts information from Terraform.
- Example: Suppose we need public IP of instance.
  ```bash
  # Resource
  
  resource "aws_instance" "web" {
    ami  =  "ami-xxxxxx"
    instance_type = "t3.micro"
  }
- Output:
  ```bash
  output "instance_public_ip" {
    value = aws_instance.web.public_ip
  }
  ```
- After apply: ```instance_public_ip = "3.x.x.x"```

## Locals
- Local values are used to define reusable values within a Terraform configuration.
- Example:
  ```bash
  locals {
    environment = "dev"
    project = "myapp"
  }
  ```
- Use:
  ```bash
  resource "aws_s3_bucket" "app" {
    bucket = "${local.project}-${local.environment}"
  }
  ```
- Result: ```myapp-dev```

## Variable vs Local
- **Variable**
  External input: Value can come from the outside.
    ```
    var.environment
    ```
- **Local**
  Internal calculation/value: It is defined within the configuration.
    ```
    local.environment
    ```
- Example:
  ```bash
  variable "environment" {
    type = string
  }
  locals {
    bucket_name = "myapp-${var.environment}"
  }
  ```
  
  > Variable = user/input.
  >
  > Local = Calculated/reusable value within Terraform.

