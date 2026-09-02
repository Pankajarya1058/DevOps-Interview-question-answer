### Q1. Can you explain how you troubleshoot a Linux server when an application is not working?

- First, I will check the basic connectivity and DNS resolution. Then I connect to the server via SSH and test the application locally using curl.
- If the application is not responding. I check whether the required port is listening using ss -tulnp. I also check the service status and application logs to understand the actual issue.
- If the application is working locally but not accessible from outside, I check the AWS Security Group, Nginx or load balancer configuration, and the required port.
- I also check the server resources like CPU, memory, and disk space, because resource issues can affect the application.
- If the service has failed, I first check the logs and identify the reason. After getting the required approval, I restart the service and monitor it to make sure the issue is resolved.
- Finally, I try to identify the root cause instead of only restarting the service.

```bash
# Remember this Structure.

1. Check connectivity
        ↓
2. Check service/application
        ↓
3. Check port
        ↓
4. Check logs
        ↓
5. Check dependencies/resources
        ↓
6. Fix the issue
        ↓
7. Verify
        ↓
8. Find root cause
```

### Q2. What is the difference between a Docker container and a Kubernetes Pod?

- A Docker container is the running instance of the Docker image where the application runs
- A Kubernetes Pod is the smallest deployable unit in Kubernetes. A Pod can contain one or more containers, and the containers inside the same Pod share the same network and storage resources.
- In simple words, Docker is used to run containers, while Kubernetes uses Pods to manage and run containers in a cluster.

### Q3. What is EKS, and why would an organization use EKS instead of managing Kubernetes completely by itself?
- EKS stands for Elastic Kubernetes Service. It is a managed Kubernetes service provided by AWS.
- The main advantages is that AWS manages the kubernetes control plane for us, so we don't have to manage the control-plane infrastructure ourselves.
- For the worker side, we can use managed node groups or Fargate. Fargate provides serverless compute, so we don't have to manage the underlying servers for those workloads.
- So, Organizations use EKS because it reduces kubernetes infrastructure management and allow the team to focus more on deploying and managing applications.

### Q4. Suppose a Kubernetes Pod is showing *CrashLoopBackOff*. How would you troubleshoot it?
- If a Pod in CrashLoopBackOff, it means the container is starting and then repeatedly failing or exiting.
- First, I will check Pod status and events using kubectl describe pod. Then I check the application logs using kubectl logs. If the container is already restarted. I also check the previous container logs using kubectl logs --previous.
- Then, I check the container configuration, such as environment variables, Secrets, ConfigMaps, the startup command, and resource limits.
- I also check whether there was any recent deployment or configuration change.
- After identify the root cause, I fix the issue and monitor the Pod to make sure it becomes stable and stays in the Running state.

### Q5. Explain what a CI/CD pipeline is, and walk me through a typical CI/CD pipeline that you have worked with.
- CI/CD stands for Continuous Integration and Continuous Delivery or Continuous Deployment.
- A CI/CD pipeline automate the process of taking the code from development to deployment.
- 
- In a typical pipeline, I have worked with, first we checkout the code from Git. Then we perform the security and dependency checks, build the application, build the Docker image and run tests.
- After the checks are successful, we deploy the application to the pre-production environment. we verify the deployment, and after the required approval, we deploy it to production.
- The main benefit of CI/CD is that it makes the build and deployment process more consistent, faster, and less dependent on manual work.

### Q6. Suppose a Jenkins pipeline has suddenly started failing during the deployment stage. How would you troubleshoot it?
- If a Jenkins pipeline fails during the deployment stage. first I check Jenkins console output and identify the exact step where the failure happened.
- Then I check the error message and understand whether the issue is related to credentials, network connectivity, permissions, the Docker image, configuration, or the target environment.
- If the deployment is to Kubernetes, I also check the deployment and Pod status, events and logs. If it is related to AWS, I check the AWS error and required permissions.
- After identify the root cause, I fix the issue and run the pipeline again. Finally, I verify that the deployment is successful and monitor the application after deployment.



