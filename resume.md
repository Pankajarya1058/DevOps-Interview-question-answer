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
- In a typical pipeline, I have worked with, first we checkout the code from Git. Then we perform the security and dependency checks, build the application, build the Docker image and run tests.
- After the checks are successful, we deploy the application to the pre-production environment. we verify the deployment, and after the required approval, we deploy it to production.
- The main benefit of CI/CD is that it makes the build and deployment process more consistent, faster, and less dependent on manual work.

### Q6. Suppose a Jenkins pipeline has suddenly started failing during the deployment stage. How would you troubleshoot it?
- If a Jenkins pipeline fails during the deployment stage. first I check Jenkins console output and identify the exact step where the failure happened.
- Then I check the error message and understand whether the issue is related to credentials, network connectivity, permissions, the Docker image, configuration, or the target environment.
- If the deployment is to Kubernetes, I also check the deployment and Pod status, events and logs. If it is related to AWS, I check the AWS error and required permissions.
- After identify the root cause, I fix the issue and run the pipeline again. Finally, I verify that the deployment is successful and monitor the application after deployment.

### Q7. What is the difference between Prometheus and Grafana, and how have you used them for monitoring?
- Prometheus and Grafana both are used for monitoring, but they have different purpose.
- Prometheus collect and stores metrics from servers and applications. we can query those metrics using PromQL.
- For Linux servers, we can use Node Exporter to expose system metrics such as CPU, memory and disk usage. Prometheus scrapes those metrics.
- Grafana is mainly used for visualize the metrics in dashboards. I have used Grafana dashboards to monitor server and application health and identify resource or availability issues.
- So, in simple words, Prometheus collects and queries the metrics, while Grafana helps us visualize those metrics.

### Q8. What is a reverse proxy, and why would you use Nginx as a reverse proxy in front of an application?
- A reverse proxy sits between the client and backend application. The client sends the request to Nginx, and Nginx forwards the request to the backend server.
- For example, my application is running 5000, I can configure Nginx to listen on port 80 or 443 and forward the request to the application on port 5000.
- We use Nginx as a reverse proxy because it can hide the backend from direct access, handle SSL/TLS, and also provide load balancing when we have multiple backend servers.

### Q9. Suppose a Linux server is running very slowly. How would you troubleshoot the performance issue?
- If a Linux server is running very slowly. first I will check the overall system health by using commands like top, uptime, and free -h.
- I check the CPU and load average, then identify which process is consuming high CPU using ps aux --sort=-%CPU. for example, I use ps aux --sort=-%MEM.
- I also check the disk space using df -h and disk I/O if required. Then I check the system and application logs to identify the root cause.
- If I identify the problematic process or application, I investigate it first, If a restart is required, I take the required approval and restart it.
- After that, I monitor the CPU, memory and application health to confirm that the server has returned to stable state.

### Q10. You receive an alert that an application running on AWS EKS is down. As a Linux/System Engineer supporting the environment, what steps would you take to troubleshoot and restore the application?
- If I receive an alert that an application running on EKS is down. first I would acknowledge the alert and check the monitoring dashboard to understand the impact.
- Then I would check the EKS cluster and Kubernetes resources. I would check the nodes, deployments, Pods, and services to identify where the problem is.
- For example, I would use commands like kubectl get nodes, kubectl get pods, and kubectl get deployments. If a Pod is failing, I would check its events and logs using kubectl describe and kubectl logs.
- If the Kubernetes resources look healthy, I would check the application endpoint, Nginx or load balancer, and AWS-related services.
- I would also checks CloudWatch and CloudTrail for relevant errors or recent changes.
- After identifying the root cause, I would take the required action, restore and application and monitor it to confirm that it is stable. Finally, I would document the incident and root cause.

### Q11. What is the difference between EC2, ECS, and EKS in AWS? When would you use each one?
EC2, ECS, and EKS are used for different purpose.
- EC2 is basically a virtual server in AWS. We can install OS, applications, Docker, etc. and manage the server by ourselves.
- ECS is AWS native container orchestration service. We use ECS when we want to run and manage Docker container but we don't need Kubernetes.
- EKS is AWS managed Kubernetes service. We use EKS when our application needs kubernetes features like Pods, deployments, services, scaling etc.

### Q12. Suppose an EC2 instance is running, but you are unable to connect to it using SSH. How would you troubleshoot the issue?
- First, I will check the AWS console to confirm that the instance is running and has passed health checks.
- Next, I will verify whether port 22 (SSH) is allowed in the Security Group and NACL. I will also check the route table and network connectivity.
- If everything is correct on the AWS side, I will log in to the server using SSM or console access. Then, I will check if the SSH service is running and listening on port 22.
- I will also examine the OS firewall and SSH logs to understand the actual issue.
- If there is a problem with the SSH service, I will first identify the root cause. If a restart is necessary, I will restart the service after obtaining the required approval and finally verify SSH connectivity.

### Q13. What is the difference between a Security Group and a Network ACL (NACL) in AWS?
- Security Group and NACL both act as a virtual firewall to control network traffic in AWS.
- The main difference is that Security Group is stateful and works mainly at the instance or network interface level.
- NACL is stateless and works at the subnet level.
- In Security Group, we can define only allow rules, not deny rules. In NACL we can define both allow and deny rules.
- In simple words, Security Group provides instance level security, while NACL provides subnet level security.

### Q14. What happens when you type a URL like https://example.com in your browser? Explain the process from DNS resolution until the web page is displayed.
- When we enter https://example.com in the browser, firstly the browser does DNS resolution to find the IP addresses of the domain.
- After getting the IP, the browser establishes a TCP connection with that server. Since this is HTTPS, TLS handshake also occurs to establish a secure connection.
- After that the browser sends HTTP request to the server. The server processes the request and returns the HTTP response to the browser.
- Finally, browser response can be received by HTML, CSS, Javascript etc. which processes and displays the web page on the screen.

  > **Simple flow:** URL → DNS → IP → TCP → TLS → HTTP Request → Server Response → Browser renders page.
