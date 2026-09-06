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
    > Variable = user/input.
    >
    > Local = Calculated/reusable value within Terraform.
- Example:
  ```bash
  variable "environment" {
    type = string
  }
  locals {
    bucket_name = "myapp-${var.environment}"
  }
  ```
  If environment = dev then local.bucket_name = myapp.dev

## Data Sources
- Suppose there is already a VPC in AWS. You don't want to create a new VPC using Terraform. You need information about the existing VPC; that's when you use a Data source.
- Interview answer: A data source allows Terraform to read information about existing infrastructure or external data without managing the lifecycle of that resource.

## Resource vs Data Source
- **Resource**
  - Terraform create/manage resources.
    ```bash
    resource "aws_vpc" "main" {
      cidr_block = "10.0.0.0/16"
    }
    ```
- **Data Source**
  - Terraform reads information from existing infrastructure.
    ```bash
    data "aws_vpc" "existing" {
      id = "vpc-12345"
    }
    ```
    Then: ```data.aws_vpc.existing.id```

## Expressions
- An expression is a way to calculate or reference values ​​in Terraform.
- Expressions help Terraform calculate dynamic values ​​and settings.
- Example:
  ```
  var.instance_type
  ```
  ```
  aws_instance.web.id
  ```
  ```
  "${var.environment}-server"
  ```
  ```
  var.environment == "prod"
  ```

### Common expressions
```
# Reference

var.region
```
```
# Resource reference

aws_instance.web.id
```
```
# Arithmetic

var.instance_count * 2
```
```
# Comparison

var.environment == "prod"
```
```
# Conditional

var.environment == "prod" ? "large" : "small"
```

## Functions
- Terraform has built-in functions that help manipulate values.
- Example: lower("HELLO")  -> hello
- Example: upper("terraform") -> TERRAFORM

## Common Terraform functions
- length() -> length(["a", "b", "c"]) -> 3
- join() -> join("-", ["dev", "web", "01"]) -> dev-web-01
- split() -> split(",", "a,b,c") -> ["a", "b", "c"]
- lookup() -> lookup(var.instance_types, "dev", "t3.micro")
- concat() -> Lists combine karne ke liye.
- toset() -> List ko set mein convert karne ke liye
- tomap() -> Map conversion ke liye.

## Conditional Expressions
- Syntax: ``` condition ? true_value : false_value```
- Practical Example:
  ```bash
  variable "environment" {
    type = string
  }
  ```
  ```bash
  resource "aws_instance" "web" {
    ami = "ami-xxxxxxxx"

    instance_type = var.environment == "prod"
      ? "t3.large"
      : "t3.micro"
  }
  ```
  ```bash
  For Dev

  environment = "dev"
       ↓
  t3.micro
  ```
   ```bash
  For prod

  environment = "prod"
       ↓
  t3.large
  ```

## Interview Questions

### Q1. What is a Terraform variable?

### Q2. How do you reference a variable?

### Q3. What is terraform.tfvars?

### Q4. Difference between variables.tf and terraform.tfvars?

### Q5. What is an output?

### Q6. Why do we use locals?

### Q7. What is a data source?

### Q8. Difference between resource and data source?

### Q9. What is a Terraform function?

### Q10. What is a conditional expression?

### Q11. What are different ways to pass variable values?

### Q12. What happens if a variable has no default value?

### Q13. Difference between variable and local?

### Q14. Difference between variable and output?

### Q15. Difference between resource and data source?

### Q16. What is the difference between terraform.tfvars and dev.tfvars?

### Q17. How can you use different values for dev and prod?

### Q18. How do you conditionally choose an EC2 instance type?

### Q19. What are Terraform expression types?

### Q20. Give examples of commonly used Terraform functions.


   
