
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
- Terraform maintains information about the infrastructure in the Terraform state.
- Conceptually:
  ```
  Terraform Configuration
        +
  Terraform State
        +
  Real Infrastructure
        ↓
  Terraform determines changes
  ```
- Example:
  ```
                   Terraform
                    |
       +------------+------------+
       |                         |
   Configuration              State
       |                         |
       +------------+------------+
                    ↓
              Compare/Refresh
                    ↓
              Real Infrastructure
  ```

## terraform.tfstate
- In a default local setup, the Terraform state is usually stored in the ```terraform.tfstate``` file.
- Example project:
  ```
  terraform-project/
  │
  ├── main.tf
  ├── variables.tf
  ├── outputs.tf
  └── terraform.tfstate
  ```
- The state file is in a format similar to JSON.
- Example conceptually:
  ```
  {
    "resources": [
      {
        "type": "aws_instance",
        "name": "web"
      }
    ]
  }
  ```
  > Note: The structure of the actual state file is quite detailed, so manual editing should be avoided.

## What information does the Terraform state contain?
- The state contains information about Terraform-managed infrastructure.
- For Example:
  ```
  Resource
   ↓
  aws_instance.web
   ↓
  AWS instance ID
   ↓
  Attributes
   ↓
  Dependencies / metadata
  ```
- Exact contents vary according to resource/provider and terraform version.

### Important: The state and infrastructure are not the same thing.
- ``` terraform.tfstate ≠ AWS EC2```
- The state is not AWS EC2.
- The state has records/knowledge about the infrastructure that it manages.
- Real infrastructure is in AWS.
  ```
  Terraform State
      ↓
  Terraform ki known information

     AWS
      ↓
  Actual infrastructure
  ```
  
