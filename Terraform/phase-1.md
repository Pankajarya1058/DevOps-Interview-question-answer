## What is Terraform?
- Terraform is a Infrastructure as Code (IaC) tool, developed by HashiCorp.
- It is used to provision and manage infrastructure using configuration files.
- Terraform follows a declarative approach, where we define the desired state and Terraform determines the required changes.

> Terraform = Manage infrastructure as Code.

## What is IaC?
Infrastructure as Code means managing and provisioning infrastructure using code or configuration files instead of manually creating resources through a cloud console.

## Terraform vs CloudFormation
- Terraform is a multi-cloud infrastructure as Code tool developed by HashiCorp. It uses HCL and maintains state file.
- CloudFormation is AWS's native Infrastructure as Code service. It uses YAML or JSON and manages infrastructure through stacks.

  > Terraform = Multi-cloud / multi-platform
  
  > CloudFormation = AWS-focused

  **Ques. If interview ask "If you are working only with AWS, why would you choose Terraform over CloudFormation?"**
  
  Ans. Terraform provides a consistent workflow across multiple platforms, has a large provider ecosystem, and its HCL syntax and module ecosystem make infrastructure reusable and maintainable.

## Terraform Architecture
```bash
                Terraform
                    |
              Terraform CLI
                    |
              Configuration
                (.tf files)
                    |
                Provider
                    |
                AWS API
                    |
       +------------+-------------+
       |            |             |
      EC2          VPC           S3
```

## Terraform Workflow
Basic workflow:
```bash
Write Code
    ↓
terraform init
    ↓
terraform fmt
    ↓
terraform validate
    ↓
terraform plan
    ↓
terraform apply
```

### terraform init
It initialize the project.
- Download provider.
- Initialize the backend.
- Download Modules.

### terraform fmt
- It arranges the terraform code in a standard format.

### terraform validate
- Checks whether the configuration is valid or not.

### terraform plan
- It tells: What changes will I make when I apply?
- The plan does not change the actual infrastructure.

### terraform apply 
- It actual changes infrastructure.

### terraform destroy
- It destroys Terraform-managed infrastructure.

## Provider
- The provider acts as a bridge between Terraform and external platforms.

  Terraform needs to communicate with AWS:
  ```bash
  Terraform
    ↓
  AWS Provider
    ↓
  AWS API
    ↓
  AWS Resources
  ```
- Example:
  
  Here, the AWS provider helps Terraform to communicate with AWS.
  ```bash
  provider "aws" {
    region = "ap-south-1"
  }
  ```

## Resource
- A resource is the actual infrastructure object that Terraform creates or manages.
- Example:
  ```bash
  resource "aws_instance" "web" {
    ami           = "ami-xxxxxxxx"
    instance_type = "t2.micro"
  }

  # aws_instance is a resource type, which means AWS EC2 instance.
  # web is a terraform local name of resource. That's why aws_instance.web will refer to resource to go inside terraform.
  ```
- Another example:
  ```bash
  resource "aws_s3_bucket" "my_bucket" {
    bucket = "my-demo-bucket"
  }

  # aws_s3_bucket is a resource type.
  # my_bucket is a local resource name.
  ```

## Provider vs Resource
- **Provider:** Which platform should I communicate with?
- **Resource:** What needs to be created or managed on that platform?

## Revision
1. Terraform is an IaC tool by HashiCorp.
2. IaC means managing infrastructure through code.
3. Terraform follows a declarative approach.
4. We define desired state, not step-by-step instructions.
5. Provider connects Terraform with platforms like AWS.
6. Resource represents actual infrastructure.
7. terraform init initializes the Terraform project.
8. terraform plan shows proposed changes.
9. terraform apply applies those changes.
10. terraform destroy removes Terraform-managed resources.

## Interview Questions
#### Q1. What is Terraform?
#### Q2. What is Infrastructure as Code?
#### Q3. What is a Terraform provider?
#### Q4. What is a Terraform resource?
#### Q5. What is terraform init?
#### Q6. What is terraform plan?
#### Q7. What is terraform apply?
#### Q8. What is terraform destroy?
#### Q9. What is Terraform's configuration language?
#### Q10. Is Terraform declarative or imperative?
#### Q11. Terraform vs CloudFormation?
#### Q12. Terraform vs manual provisioning?
#### Q13. What happens during terraform init?
#### Q14. What is the difference between terraform plan and terraform apply?
#### Q15. What is the difference between provider and resource?
#### Q16. Why is Terraform called declarative?
#### Q17. How does Terraform communicate with AWS?
#### Q18. What are the major components of Terraform architecture?

