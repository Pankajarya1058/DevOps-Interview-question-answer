
## Load Balancer Fundamentals.

### What is a Load Balancer?
- A load balancer is a component or service that distributes incoming traffic across multiple backend servers or targets to improve availability, scalability, and fault tolerance.

### Important types of Elastic Load Balancing (ELB) family in AWS.
- **ALB** → HTTP/HTTPS applications.
- **NLB** → TCP/UDP, high-performance/network workloads.
- **GWLB** → network/security appliances.
- **CLB** → older/legacy option.

### Why do we need a Load Balancer?
1. **High Availability.**
   - If one server fails so traffic forward to the healthy server.

2. **Traffic Distribution.**
   - Instead of sending all requests to one server. Traffic distributes on multiple targets.

3. **Scalability.**
   - If traffic increase so Auto scaling can launch instances additionally.
   - ALB can distribute traffic to the new healthy targets.
  
4. **Health Checks.**
   - Load balancer periodically check "Target is healthy or not."
   - and remove unhealthy target from traffic.
  
5. **SSL/TLS Termination.**
   - Client → HTTPS → ALB → HTTP → EC2
   - It can be reduce TLS handling burden from backend servers.

- **Interview answe**r - We use a load balancer for high availability, traffic distribution,, scalability, health-based routing, and in some architectures TLS termination.

**Important point**
- Load Balancer does not choose random server directly.
- Selection depends on things such as:
  - Load-Balancer type
  - Listener
  - Listener rules
  - Target group
  - Target health
  - Routing algorithm/configuration
 
### Client → Load Balancer → Backend Flow
- Suppose user hit ```https://example.com``` in browser.
  
  **Step 1 — DNS**
  - Client resolves domain.
  - example.com -> ALB DNS.
 
  **Step 2 — Connection to Load Balancer.**
  - Client connect to ALB listener.
  - Example: ```HTTPS :443```
 
  **Step 3 — Listener receives request.**
  - ALB has a listener configured ```HTTPS :443```
 
  **Step 4 — Listener Rule.**
  - ALB checks: ```Path?```, ```Host?```, ```Headers?```, ```Query?```
 
  **Step 5 — Target Group**
  - ALB select appropriate target group.
 
  **Step 6 — Health Check**
  - ALB identify healthy targets.

  **Step 7 — Backend Response.**
  - EC2 —> ALB —> Client
 
  **Complete flow**
  ```
         Browser
           |
           | HTTPS :443
           ↓
          DNS
           |
           ↓
          ALB
           |
           | Listener
           ↓
       Listener Rule
           |
           ↓
       Target Group
           |
           ↓
       Healthy Target
           |
           ↓
       Application
  ```

### Difference between Reverse Proxy and Load Balancer
| Feature                       | Reverse Proxy | Load Balancer   |
| ----------------------------- | ------------- | --------------- |
| Client ke saamne              | Yes           | Yes             |
| Backend ko hide kar sakta hai | Yes           | Yes             |
| Traffic distribution          | Possible      | Core purpose    |
| Multiple servers              | Possible      | Designed for it |
| SSL termination               | Possible      | Possible        |
| Routing                       | Yes           | Yes             |
| Health checks                 | Possible      | Built-in/common |
| Example                       | Nginx         | AWS ALB/NLB     |

**Important:** A load balancer can also behave like a reverse proxy, especially an ALB. But every reverse proxy necessarily isn't a dedicated load balancer.

### Public vs Internal Load Balancer
- In AWS, Load Balancer can be internet-facing or internal.

  **Internet-facing Load Balancer**
  ```
      Internet
         |
         v
      Internet-facing ALB
         |
         v
      Private EC2
  ```
  For example, public website:
  ```
    www.example.com
         |
         v
    Internet-facing ALB
         |
         v
    Web servers
  ```
  - ALB generally placed in public subnets.

  **Internal Load Balancer**
  - Internal ALB/NLB are not directly accessible via internet.
  - Use cases:
    - Internal microservices.
    - Private APIs.
    - Application tiers.
    - Backend services.
    - Service-to-service communication.
  - Typical architecture:
    ```
              Internet
                  |
                  v
              Public ALB
                  |
                  v
              Frontend
                  |
                  v
             Internal ALB
                  |
             +----+----+
             |         |
             v         v
           API-1     API-2
    ```
**Public vs Internal — Quick Comparison**
| Feature             | Internet-facing | Internal                  |
| ------------------- | --------------- | ------------------------- |
| Internet accessible | Yes             | No direct internet access |
| Public IP/DNS       | Yes             | Private                   |
| Typical use         | Public web app  | Internal application/API  |
| Placement           | Public subnets  | Private subnets           |
| Example             | Website ALB     | Backend API ALB           |

---

### AWS Elastic Load Balancing
- AWS ELB service distributes incoming traffic to multiple targets.

#### There are four types of load balancer in AWS.
| Type     | Layer / Traffic                           | Main Use                                |
| -------- | ----------------------------------------- | --------------------------------------- |
| **ALB**  | Layer 7 — HTTP/HTTPS                      | Web apps, APIs, microservices           |
| **NLB**  | Layer 4 — TCP/UDP/TLS                     | High-performance networking, static IP  |
| **GWLB** | Layer 3-ish / network appliance insertion | Firewalls, IDS/IPS, security appliances |
| **CLB**  | Older Layer 4/7                           | Legacy applications                     |

**1. ALB — Application Load Balancer**
   - The ALB makes routing decisions by inspecting HTTP/HTTPS requests at the application level.
   - That is why the ALB is called a Layer 7 load balancer.
   ```
                    Client
                      |
                 HTTPS :443
                      |
                      v
              +---------------+
              |      ALB      |
              +-------+-------+
                      |
             Listener / Rules
                      |
          +-----------+-----------+
          |                       |
          v                       v
      /api/*                  /web/*
          |                       |
          v                       v
     API Target Group       Web Target Group
          |                       |
       EC2/ECS                  EC2/ECS
   ```
**2. NLB — Network Load Balancer**
   - NLB operate on Layer 4.
   - Main protocols: ```TCP```, ```UDP```, and ```TLS```
   - ALB understands HTTP requests, whereas NLB primarily handles traffic based on network-level connection information.
   ```
         Client
           |
          TCP :443
           |
           v
         +-------+
         |  NLB  |
         +---+---+
           |
           +------ Server 1
           |
           +------ Server 2
   ```
   When we use NLB?
   - Suppose your application is not HTTP-based.
     ```
        Client
         |
        TCP
         |
         v
        NLB
         |
         +---- Application-1
         |
         +---- Application-2
     ```
     ```
     Client
      |
     UDP
      |
      v
     NLB
     ```
**4. GWLB — Gateway Load Balancer**
- GWLB is used to deploy, scale, and manage virtual network/security appliances such as firewalls and IDS/IPS systems.
- For example: ```Firewall```, ```IDS```, ```IPS```, ```Deep packet inspection appliance```, and ```Network security appliance```
  ```
                  Client
                    |
                    v
                  GWLB
                    |
          +---------+---------+
          |                   |
          v                   v
       Firewall-1          Firewall-2
          |                   |
          +---------+---------+
                    |
                    v
              Application
   ```
   
  
    


  
  
