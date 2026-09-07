### Q1. What is Amazon ECS?
- Amazon ECS stands for Elastic Container Service. It is AWS-native container orchestration service.
- ECS used to deploy, run, manage, and scale Docker containers.
- In ECS, we can run containers using either the Fargate or EC2 launch type.

### Q2. What are the different launch types in Amazon ECS?
- Amazon ECS primarily offers two launch types: EC2 and Fargate.
- With the **EC2 launch type**, ECS tasks run on managed EC2 instances, meaning we are responsible for managing the EC2 infrastructure, patching, and capacity.
- With the **Fargate launch type**, AWS manages the underlying infrastructure. We only need to define the CPU, memory, and container configuration in the task definition. That is why Fargate is also referred to as serverless container compute.

### Q3. What is an ECS Cluster?
- An ECS cluster is a logical grouping where we organize and run our ECS services and tasks. Simply put, the cluster provides an environment in which our containers run.
- If we are using the EC2 launch type, the cluster can contain ECS container instances. If we are using Fargate, we do not need to manage EC2 instances; Fargate manages the infrastructure.

### What is an ECS Task Definition?
- An ECS Task Definition is a blueprint or configuration file that defines how the container will run. It specifies parameters such as the container image, CPU, memory, port mapping, environment variables, logging configuration, and IAM roles.
- When ECS needs to run a task, it creates the container based on the Task Definition.
