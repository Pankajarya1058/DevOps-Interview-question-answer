
## ECS Cluster Setup

### 1. Introduction
- In Amazon ECS, when you create a cluster using the AWS Management Console, something important happens behind the scenes
- Even if you don’t select any infrastructure, Fargate and Fargate Spot are automatically included
- This means, whether you choose it or not, you can always run serverless containers using Fargate
- If you want to completely avoid Fargate and use only EC2 or external capacity, the AWS Console won’t allow you to do that directly. For that, you must use the AWS CLI.

### 2. Fargate and Fargate Spot

- **Fargate**
  - A fully-managed serverless container platform
  - No need to manage EC2 instances — AWS handles provisioning, scaling, patching, and availability
  - Ideal for production workloads where stability and high availability are required
 
- **Fargate Spot**
  - A cheaper version of Fargate (up to 70% cost savings)
  - Uses spare capacity from AWS.
  - May be interrupted anytime, so best for batch jobs, dev/testing, and fault-tolerant workloads
 
- **Important Behavior**
  - Even if you add EC2 during cluster creation or not, Fargate and Fargate Spot are still available by default
  - This means every ECS cluster created from the AWS Console is always ready for hybrid deployment — you can run workloads on Fargate, Fargate Spot, or EC2 anytime based on your needs.

- **Important Note**
  - Once your ECS cluster is created — whether it includes Fargate, Fargate Spot, or EC2 — you can launch your task (container) or service on any one of these infrastructure types
  - You must explicitly select the launch type (Fargate, Fargate Spot, or EC2) during task or service creation
  - A single task cannot run on multiple infrastructures at the same time
  - You can run multiple copies of a task across different infrastructures by creating separate services or task definitions for each

### 3. Adding EC2 Infrastructure to ECS Cluster

- **Introduction**
  - In addition to Fargate and Fargate Spot (which are always available), you can also use EC2 instances as infrastructure for running containers
  - This gives you more control over the compute layer — including instance type, AMI, storage, and networking
  - Two Ways to Add EC2 Infrastructure
    - Option 1: Add EC2 During Cluster Creation
    - Option 2: Add EC2 Manually After Cluster Creation
      
- **Option 1: Add EC2 During Cluster Creation**
  - While creating the cluster from the AWS Console, you can select EC2 infrastructure.
  - AWS will automatically create.
    - An Auto Scaling Group
    - A Launch Template
    - ECS-optimized EC2 instances
  - You just need to provide
    - VPC, Subnets
    - Instance type, AMI
    - Key Pair
    - Security Group
    - Min, Max, and Desired instance count
  - Recommendation
    - Use this option if you plan to run workloads on EC2 from the beginning.
    - It’s easier and faster because AWS provisions everything for you
   
- **Option 2: Add EC2 Manually After Cluster Creation**
  - **Introduction**
    - If you didn’t select EC2 while creating the cluster, you can still add it later in two ways.
      - Launch individual EC2 instances manually:
      - Create your own Auto Scaling Group manually
  - **Launch individual EC2 instances manually**
    - **Step 1: Launch EC2 Instance**
      - Go to EC2 → Launch Instance
      - Choose Amazon ECS-Optimized Amazon Linux 2 AMI
      - Choose instance type (e.g., t3.micro)
      - Select the appropriate VPC & subnet (should match ECS networking setup)
        
    - **Step 2: Attach IAM Role**
      - To allow your EC2 instance to register itself with an ECS cluster, it must have permissions to communicate with ECS. This is done by attaching an IAM Role
      - How to Attach IAM Role
        - In the EC2 Dashboard, select your instance.
        - Click on Actions → Security → Modify IAM Role.
        - In the drop-down, choose the role ecsInstanceRole
        - Then click Update IAM role
        - Important Note
          - If in past you created your ECS cluster with EC2 infrastructure selected, AWS automatically created the ecsInstanceRole for you
          - But if you created your cluster without EC2, this role may not exist
      - If ecsInstanceRole Doesn’t Exist – Create It Manually
        - Go to IAM → Roles → Create role
        - Choose AWS Service → Use Case: EC2
        - Click Next: Permissions Subtopic
        - Search and attach the policy ➤ AmazonEC2ContainerServiceforEC2Role
        - Click Next → Name the role ➤ ecsInstanceRole
        - Click Create role
        - Once created, go back to EC2 → Actions → Security → Modify IAM Role and attach it.
       
    - **Step 3: Manually Configure ECS Agent via SSH**
      - SSH into the EC2 instance ssh -i your-key.pem ec2-user@<your-ec2-public-ip>
      - Open the ECS config file sudo vi /etc/ecs/ecs.config
      - Add this line ECS_CLUSTER=your-cluster-name 🔁 Replace your-cluster-name with the exact name of your ECS cluster
      - Save and exit ESC :wq
      - Restart the ECS agent sudo systemctl restart ecs
     
    - **Step 4: Verify EC2 is Registered with ECS Cluster**
      - Go to ECS → Clusters
      - Click your cluster name
      - Go to the Infrastructure tab
      - If everything is correct, your EC2 instance should appear here as a container instance

  - **Create your own auto scaling group manually and add it to ecs**
    
    - **Step 1: Create IAM Role for EC2 (ecsInstanceRole)**
      - Go to IAM > Roles → Click Create Role
      - Trusted entity Select EC2
      - Attach this policy AmazonEC2ContainerServiceforEC2Role
      - Name the role ecsInstanceRole
        
    - **Step 2: Create a Launch Template**
      - Go to EC2 > Launch Templates
      - Click Create launch template
      - Fill the details
        - Name: ecs-launch-template
        - AMI: Use Amazon ECS-Optimized Amazon Linux 2 AMI
        - Instance type: t2.micro/t3.micro
        - Key pair: Choose existing or create new one
        - IAM instance profile: Select ecsInstanceRole
        - Security Group: Open port 80 (for web app), and optionally 22 (for SSH)
        - Under Advanced details > User data, paste this script
          - #!/bin/bash
            echo ECS_CLUSTER=your-cluster-name >> /etc/ecs/ecs.config
          - Replace your-cluster-name with your actual ECS cluster name
        - Click Create launch template
       
     - **Step 3: Create Auto Scaling Group**
       - Go to EC2 > Auto Scaling Groups
       - Click Create Auto Scaling Group
       - Name ecs-asg
       - Choose your launch template created in step 2 ecs-launch-template Next
       - Choose Default VPC and Subnets where your ECS cluster runs Next
       - Integrate with other services - optional No need to configure anything here Next
       - Configure group size and scaling - optional Desired Capacity Next
       - Add notifications - optional No need to configure anything here Next
       - Add tags - optional No need to configure anything here Next
       - Review Click Create Auto Scaling Group
      
    - **Verification After Auto Scaling Group Creation**
      - Once you create the Auto Scaling Group (ASG) and launch EC2 instance.
      - Go to ECS > Your Cluster > ECS Infrastructure Tab Container instances.
      - You should see your EC2 instance listed Status ACTIVE
        
    - **What happens if you don't register ASG with ECS Cluster ?**
      - No managed scaling: ECS cannot automatically increase/decrease EC2 count
      - No automated draining: ECS won’t cleanly drain tasks before terminating instances
      - No advanced placement strategies: Like capacity-aware task scheduling
        
    - **When should you register ASG (as Capacity Provider)?**
      - When you want managed scaling
      - When you want ECS to control EC2 instances
      - For production setups with cleaner lifecycle and auto-drain
     
    - **Final Step: Register Auto Scaling Group with ECS Cluster**
      - **Step 1: Create a Capacity Provider**
        - Go to ECS > Clusters
        - Click your existing cluster name
        - Select Infrastructure Tab Click Create
        - Capacity provider name EC2-ASG-Provider
        - Auto Scaling group Select your ASG
        - Create
      - **Step 2: Attach Capacity Provider to ECS Cluster**
        - After capacity provider is created, go back to ECS > Clusters > your-cluster-name
        - Click Update Cluster
        - Under Capacity providers, add the newly created capacity provider EC2-ASG-Provider
        - Click Update
      - **Now Your ASG is Registered with ECS**
