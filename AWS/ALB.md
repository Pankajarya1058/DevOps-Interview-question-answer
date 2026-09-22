
## Application Load Balancer (ALB)

```
                         Internet
                            |
                            v
                    ALB DNS Name
                            |
                +-----------+-----------+
                |                       |
             AZ-1                    AZ-2
                |                       |
          +-----v-----+           +-----v-----+
          |    ALB    |           |    ALB    |
          +-----+-----+           +-----+-----+
                |                       |
             Listener                Listener
             :80/:443                :80/:443
                |                       |
                +-----------+-----------+
                            |
                     Listener Rules
                            |
                     Target Group
                    /      |      \
                   /       |       \
                  v        v        v
               EC2-1    EC2-2    EC2-3
                :80       :80       :80
```
>Important: ALB is a regional service and is normally deployed by selecting subnets across multiple Availability Zones.

### 1. ALB Architecture
   - ALB mainly:
     - receive client connection.
     - Accept request at listener.
     - evaluate rules.
     - Choose appropriate target group.
     - Select healthy target.
     - Forward request to backend.
   ```
   ALB
   |
   +-- Listener
         |
         +-- Listener Rules
                |
                +-- Target Group
                       |
                       +-- Targets
   ```
   Example:
   ```
       Client
        |
        v
       ALB
        |
        | HTTPS :443
        v
      Listener
        |
        | /api/*
        v
      Listener Rule
        |
        v
      API Target Group
        |
        +---- EC2-1
        +---- EC2-2
   ```

### 2. Listener
   - The Listener is the connection entry point of ALB.
   - What is configured in the listener?
     - Mainly: ```Protocol```, ```Port```, Default action```, and Listener rules```
   - Example:
     ```
     Protocol: HTTP
     Port: 80
     Default action: Forward to Web-TG
     ```
     ```
     ALB
     |
     +--- Listener :80
     |
     +--- Listener :443
     ```

### 3. Listener Rules
   - The listener reviews the request and decides where to send it.
   - Example:
     ```
     ALB
      |
     Listener :443
      |
      +----------------------------+
      |                            |
      | /api/*                     | /admin/*
      v                            v
     API Target Group          Admin Target Group
     ```
   - **Path-based routing**
     - Suppose: ```example.com/api/users
     - Rule:
       ```
       IF path = /api/*
       THEN forward → API-TG
       ```
   - **Host-based routing**
     ```
                    ALB
                     |
                 Listener
                     |
          +----------+----------+
          |                     |
      api.example.com      www.example.com
          |                     |
          v                     v
       API-TG                Web-TG
     ```
   - A listener also has a default rule. If a request does not match any specific rule.
     ```
     Request
       |
       v
     Specific rules?
       |
       No
       |
       v
     Default rule
     ```

### 4. Target Group
   - Target group is a logical collection of backend targets.
     ```
     API Target Group
       |
       +--- EC2-1
       +--- EC2-2
       +--- EC2-3
     ```
   - Listener rule select target group.
   - Target group contains information like ```Target type```, ```Protocol```, ```Port```, ```Health check```, ```Registered targets```.
   - Example:
     ```
     Target Group: web-tg

     Protocol: HTTP
     Port: 80

     Health check:
     Protocol: HTTP
     Path: /
     Port: traffic-port
     ```

### 5. Targets
   - The target is the actual backend resource to which the ALB forwards requests.
   - Depending on target group type, Target could be ```EC2 instance```, ```IP address```, ```Lambda function```
   - Target Group = logical grouping
   - Target = actual backend

### 6. health Checks
   - ALB periodically check target "Are you healthy?"
   - Health check configuration:
     - Protocol
     - Port
     - Path
     - Healthy threshold
     - Unhealthy threshold
     - Timeout
     - Interval
     - Success codes

### 7. Target Types
   - In ALB Target Group, Target could be in different types.

   **A. Instance**
   ```
      Target Group
       |
       +--- EC2-1
       +--- EC2-2
   ```

   **B. IP**
   - You can register the IP address as a target.
   - Useful for ```Containers```, ```Private IP-based workloads```, and ```Certain custom architecture```
   - Important: IP target generally private IP address hota hai within the supported networking model; arbitrary public internet IP ko simply target nahi bana dete.
   ```
      Target Group
       |
       +--- 10.0.1.10
       +--- 10.0.2.20
   ```

   **C. Lambda**
   - The ALB can route requests to the Lambda function.
   - Useful when you want HTTP/HTTPS requests to invoke serverless code without maintaining EC2 servers.

### 8. Port Mapping
   - It is not mandatory for the client-facing port and the backend target port to be the same.
   - Suppose:
     ```
     ALB Listener
     Port: 80
     ```
   - Target:
     ```
     EC2
     Application Port: 8080
     ```
   - It could be possible:
     ```
     Client
      |
      | HTTP :80
      v
     ALB
      |
      | HTTP :8080
      v
     EC2
     ```

### 9. Security Groups
   - It is a good practice to maintain separate security groups for the ALB and EC2 instances.
   - ALB Security Group
     ```
     Inbound:
     TCP 443
     Source: 0.0.0.0/0
     ```
   - EC2 Security Group
     - Instead of ```TCP 80, Source: 0.0.0.0/0```
     - Better architecture: ```TCP 80, Source: ALB-SG```
     - Meaning: Only traffic from the ALB security group is allowed on port 80.
     
### 10. Cross-Zone Load Balancing
    - A Load balancer can distribute traffic to available targets across Availability Zones.
      ```
              ALB
             /   \
           AZ-1  AZ-2
            |      |
           T1     T3
           T2     T4
           
      ```

### 11. ALB DNS
    - After creating the ALB, AWS provides a DNS name.
    - Example format: ```my-alb-123456789.ap-south-1.elb.amazonaws.com```
    - The client can reach the ALB by resolving this DNS name.
    - In Production, we usually use custom domain ```www.example.com``` and associate the DNS service, commonly route 53, with the ALB.
      ```
      www.example.com
       |
       v
      Route 53
       |
       v
      ALB
       |
       v
      Target Group
      ```

**12. ALB Connection Flow**

    - Suppose user accesses: ```https://api.example.com/users```
    
    - Step 1 — DNS
      ```
      api.example.com
         |
         v
        DNS
         |
         v
        ALB
      ```
      
    - Step 2 — ALB listener
      - Listener receive request at ALB ```HTTPS: 443```

    - Step 3 — Listener rule
      - ALB sees:
        ```
        Host: api.example.com
        Path: /users
        ```
      - Rule:
        ```
        IF host = api.example.com
        THEN forward → API-TG
        ```
        
    - Step 4 — Target Group
      ```
      API-TG
         |
         +---- EC2-1 ✓
         +---- EC2-2 ✓
         +---- EC2-3 ✗
      ```

    - Step 5 — Healthy target selection
      - ALB select healthy targets.

    - Step 6 — Backend request
      ```
      ALB
       |
       | HTTP :8080
       v
      EC2-1
       |
       v
      Application
      ```

    - Step 7 — Response
      ```
      Application
         |
         v
        EC2
         |
         v
        ALB
         |
         v
       Client
       ```

### Complete ALB Picture

```
                         CLIENT
                           |
                           | HTTPS :443
                           v
                    +-------------+
                    |     ALB     |
                    +------+------+
                           |
                       Listener
                           |
                    Listener Rules
                           |
                +----------+----------+
                |                     |
             /api/*               /web/*
                |                     |
                v                     v
           API Target Group      Web Target Group
                |                     |
          +-----+-----+          +----+----+
          |           |          |         |
          v           v          v         v
        EC2-1       EC2-2      EC2-3     EC2-4
          |           |          |         |
          +-----------+----------+---------+
                      |
                 Health Checks

```
      
    

     
   
    
   
