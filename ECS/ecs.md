## ECS (Elastic Container Service)

ECS stands for Elastic Container Service which is AWS-native container orchestration service.

---

### What is ECS Cluster?
- An ECS cluster represents a group of Docker hosts acting as a unified environment for container orchestration
- It provides the resource pool where ECS can schedule and run containerized applications.
- Crating a Cluster is required because ECS needs a centralized control point to manage task placement, resource isolation, and service scaling.
- You can run multiple services and tasks inside the ECS cluster.
- ECS supports two types of cluster infrastructure for running containers.
   - Amazon EC2
   - AWS Fargate

### Infrastructure
- #### Amazon EC2 (Self-Managed Infrastructure)
  - **Infrastructure Provisioning** - You launch and manage EC2 instances.
  -  **Control Level** - Full control over OS, instance type, Storage, AMIs.
  -  **Launch Time** - Slower(wait for EC2 boot and registration).
  -  **Billing Model** - Pay for EC2 uptime; supports On-Demand, Spot, and Savings Plans.
  -  **VPC Selection Timing** - Selected during Cluster Creation.
  -  **Auto Scaling Configuration** - ASG is created during cluster creation, but you manage instance scaling policies.
  -  **Scaling Flexibility** - Based on EC2 limits -> Scale EC2 first, then tasks.
  -  **Maintenance Responsibility** - You manage patching, scaling, instance health.
  -  **Task Placement** - ECS places tasks on EC2 instances based on available resources.
  -  **Container isolation** - Multiple containers share same EC2 instance resources.
  -  **Use Case Fit** - Best for apps needing OS-level access or GPU support.
  -  **Suitable For** - Long-running apps, OS Customizations, GPU/SSD needs.
    
- #### AWS Fargate (Fully-Managed Serverless)
  - **Infrastructure Provisioning** - AWS provisions infrastructure automatically.
  - **Control Level** - No access to underlying OS or computer layer.
  - **Launch Time** - Faster (AWS handles compute provisioning instantly).
  - **Billing Model** - Pay per vCPU and memory usage per second for each running task.
  - **VPC Selection Timing** - Selected when creating a task or service.
  - **Auto Scaling Configuration** - ECS handle scaling for Fargate tasks automatically.
  - **Scaling Flexibility** - Directly scales tasks(no infrastructure scaling needed).
  - **Maintenance Responsibility** - AWS manages all underlying infrastructure.
  - **Task Placement** - ECS directly launches tasks on Fargate compute.
  - **Scaling Flexibility** - Directly scales tasks (no infrastructure scaling needed).
  - **Container isolation** - Each tasks runs in isolated environment with its own ENI.
  - **Use Case Fit** - Best for serverless microservices, batch jobs, quick deployments.
  - **Suitable For** - Microservices, fast-scaling APIs, short-lived workloads.
 
---

### ECS Anywhere.
- ECS Anywhere is a feature of Amazon ECS that allows you to run ECS tasks on external (non-AWS) machines.
- ECS Anywhere supports the following types of external infrastructure.
  - On-premises severs (physical or virtual machines).
  - Virtual machines running in other cloud providers (e.g., Azure, GCP etc).

> NOTE: External instances using ECS Anywhere can be registered after cluster creation is complete.

#### How ECS Anywhere works.
To connect an on-premises VM (or Self-managed EC2) to an ECS Cluster using ECS Anywhere. the following components must be set up.
- **Network Connectivity to AWS APIs.**
  - The VM must have outbound internet access (or VPC endpoints if inside AWS) to connect with ECS and SSM APIs securely.
  - There's no need for inbound access or VPNs.
- **Container Runtime.**
  - The VM must have Docker (or another supported container runtime) installed and running.
  - ECS uses the runtime to pull, run, and manage containers defined in ECS task definitions.
  - Currently ECS Supports Docker and Containerd
- **SSM Agent (AWS Systems Manager Agent).**
  - This agent is required to securely register the VM with AWS Systems Manager.
  - It allows AWS to manage and control the instance remotely. Without it, the VM cannot be registered as a managed instance.
  - When the SSM Agent registers the VM using the Hybrid Activation, it automatically assumes the IAM Role specified during activation.
  - This IAM role grants necessary permissions to
    - Register the instance with ECS.
    - Connect an report to AWS Systems Manager.
    - Run ECS tasks and send heartbeat/log data.
- **ECS Agent.**
  - This ECS agent is responsible to communicating with the ECS control plane.
  - It allows the VM to join the ECS cluster and receive task definitions to run containers.
  - It ensures that the instance acts like a traditional ECS-managed instance.
    
