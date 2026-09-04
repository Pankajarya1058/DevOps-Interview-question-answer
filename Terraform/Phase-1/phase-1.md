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

### terraform apply 
- It actual changes infrastructure.

### terraform destroy
- It destroys Terraform-managed infrastructure.



### terraform validate
- Checks whether the configuration is valid or not.

### terraform plan
- It tells: What changes will I make when I apply?
- The plan does not change the actual infrastructure.
