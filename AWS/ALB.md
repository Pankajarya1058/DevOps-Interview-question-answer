
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

**1. ALB Architecture**
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

**2. Listener**
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

**3. Listener Rules**
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

**4. Target Group**
   - Target group is a logical collection of backend targets.
       
   
     
   
    
   
