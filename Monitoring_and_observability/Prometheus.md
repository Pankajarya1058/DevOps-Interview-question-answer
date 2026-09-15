
## What is Prometheus?
- Prometheus is a open-source monitoring and alerting system that collects, stores and queries time-series metrics.

- Simple flow
  ```
   Application / Server
          ↓
       Metrics
          ↓
     Prometheus
          ↓
     ┌────┴────┐
     ↓         ↓
  Grafana   Alertmanager
  ```
- Example:
  ```
  CPU = 75%
  Memory = 60%
  HTTP requests = 500/sec
  HTTP errors = 2%
  ```
- Prometheus collects these metrics and stores them over time.

### 1. Prometheus Architecture
```
                    Prometheus
                        |
          ┌─────────────┼─────────────┐
          ↓             ↓             ↓
       Targets       Exporters    Service Discovery
          |             |
          ↓             ↓
      Application    Node Exporter
      /metrics       Blackbox Exporter
                    etc.
          |
          ↓
    Prometheus TSDB
          |
     ┌────┴─────┐
     ↓          ↓
  PromQL      Alert Rules
     ↓          ↓
  Grafana    Alertmanager
```
#### Important components
- **Prometheus Server**
  - Main Components:
    - Scrapes metrics
    - Stores metrics
    - Executes PromQL
    - Evaluates alert rules

- **Exporters**
  - Expose metrics from existing systems.
  - Example:
    ```
      Linux Server
         ↓
      node_exporter
         ↓
      /metrics
         ↓
      Prometheus
    ```
- **Alertmanager**
  - It receives alerts from prometheus and sends notifications.
    ```
      Prometheus
          ↓
      Alertmanager
          ↓
      Slack / Email / PagerDuty etc.
    ```
- **Grafana**
  - Visualizes the metrics in dashboards/graphs.

### 2. Pull Model
- Prometheus generally uses a pull-based model.
- Prometheus periodically scrapes metrics from configured targets through HTTP endpoints, usually /metrics.
- Example:
  ```
  Every 15 seconds

  Prometheus
    |
    | GET /metrics
    ↓
  Server / Application
    |
    ↓
  metrics
  ```
- Suppose node_exporter is installed in server ```http://server-ip:9100/metrics```
- Prometheus ```GET http://server-ip:9100/metrics```
- Response:
  ```
  node_cpu_seconds_total ...
  node_memory_MemAvailable_bytes ...
  node_filesystem_avail_bytes ...
  ```
- Prometheus scrapes these metrics and stores them.

### 3. Targets = Jis endpoint se Prometheus metrics collect karega.
- The endpoint from which Prometheus will collect metrics.
- Example:
  ```
  scrape_configs:
  - job_name: node
    static_configs:
      - targets:
          - "192.168.1.10:9100"  # This is targets.
          - "192.168.1.11:9100"  # This is targets.
  ```
- Prometheus will scrapes these targets.

  **What is Job**
  - job_name: node
  - Job = logically is a group of similar targets.
  - Example:
    ```
    job = node
        ↓
    server1:9100
    server2:9100
    server3:9100
    ```
  - Another:
    ```
    job = nginx
        ↓
    nginx1:9113
    nginx2:9113
    ```

### 4. Exporters
- Suppose Prometheus needs metrics of Linux server such as CPU, memory, disk metrics.
- Linux does not directly expose metrics in Prometheus format.
- So we install Node Exporter and it expose system-level metrics.
  ```
  Linux Server
     ↓
  Node Exporter
     ↓
  /metrics
     ↓
  Prometheus
  ```

### Common Exporters
| Exporter                | Purpose                          |
| ----------------------- | -------------------------------- |
| **Node Exporter**       | Linux/Unix system metrics        |
| **Blackbox Exporter**   | HTTP, TCP, ICMP endpoint probing |
| **Windows Exporter**    | Windows metrics                  |
| **MySQL Exporter**      | MySQL metrics                    |
| **PostgreSQL Exporter** | PostgreSQL metrics               |
| **Nginx Exporter**      | Nginx metrics                    |


> Important: The exporter does not send data directly to Prometheus.
>
**Usually:**
```
Exporter exposes /metrics
          ↓
Prometheus scrapes /metrics
```

### 5. Labels
- The purpose of the label is to identify the metric with additional dimensions.
- Example: ```http_requests_total```
- This alone is useful, but we don't know it:
  - Which service?
  - Which method?
  - Which status?
- We can add Labels:
  ```
  http_requests_total{
    method="GET",
    status="200",
    service="payment"
  }
  ```
- Now, we can query specifically: ```service="payment"``` OR ```status="500"```

  **Real Example:**
  - Suppose:
    ```
    http_requests_total{
      service="frontend",
      method="GET",
      status="200"
    } 1000
    ```
  - OR:
    ```
    http_requests_total{
      service="frontend",
      method="GET",
      status="500"
    } 50
    ```
  - Now we filter 500 errors from PromQL. ```http_requests_total{status="500"}```

### 6. Labels and Cardinality
- Note: This is advanced important topic for SRE/Prometheus.
- Each unique label combination creates a separate time series.
- Example:
  ```
  http_requests_total{
    service="payment",
    status="500"
  }
  ```
- Different combinations:
  ```
  payment + 200
  payment + 400
  payment + 500
  frontend + 200
  frontend + 500
  ```
- Each combination can be a separate time series.

**Bad level example** ```user_id="12345"```
- If there are millions of users:
  ```
  user_id=1
  user_id=2
  user_id=3
  ...
  user_id=10000000
  ```
- Very high cardinality.
- **Result:** Prometheus can significantly increase memory/storage usage.
- Avoid high-cardinality labels such as:
  ```
  user_id
  request_id
  transaction_id
  session_id
  ```
- unless there is a very specific, carefully managed reason.

### 7. PromQL = Prometheus Query Language.
- It is used to query, filter, and aggregate stored metrics of prometheus.
- Example: ```up```
- Meaning: ```Are the targets currently up or not?```
- Result: ```up{job="node", instance="server1:9100"} 1```
- 1 = up and 0 = down


**CPU Query**
- Raw CPU metric. ```node_cpu_seconds_total```
- To calculate the CPU utilization:
  ```
  100 - (
    avg by(instance) (
      rate(node_cpu_seconds_total{mode="idle"}[5m])
    ) * 100
  )
  ```
- Conceptually:```Used CPU = CPU total - Idle CPU```

**Label filtering**
- ```http_requests_total{status="500}
- Meaning: ```Only HTTP 500 requests```
- Multiple labels:
  ```
  http_requests_total{
    service="payment",
    status="500"
  }
  ```

### 8. rate()
- rate() is commonly used to calculate the per-second average rate of increase of a counter over a specified time window.
- rate() is very important for Counter metrics.
- Suppose: ```http_requests_total``` is continuously increase: ```1000, 1100, 1200, 1300```
- We have to know: ```Requests per second kitni aa rahi hain?```
- Use: ```rate(http_requests_total[5m])
- Example result: ```25 requests/sec```

### 9. Aggregation
- Suppose we have multiple instances:
  ```
  server1 -> 100 req/sec
  server2 -> 150 req/sec
  server3 -> 200 req/sec
  ```
- Total: ```sum(rate(http_requests_total[5m])```
- Result: ```450 req/sec```
- Common aggregation operators: ```sum, avg, min, max, count```

### 10. Service Discovery
- It is difficult to manually maintain targets in production.
- In kubernetes, Pods are dynamically create/delete.
- That's where service discovery comes in.
- Prometheus can automatically discover targets from system such as:
  ```
  Kubernetes
  AWS
  Consul
  EC2
  File-based discovery
  DNS
  ```
- Kubernetes Example:
  ```
  Kubernetes API
      ↓
  Prometheus
      ↓
  Discover Pods/Services/Nodes
      ↓
  Scrape metrics
  ```
- Interview answer
  - Service discovery allows Prometheus to automatically discover monitoring targets instead of maintaining a static list of IP addresses or endpoints.
 
### 11. Recording Rules
- Suppose your CPU query is complex and it is being used 100 times in a dashboard. 
  ```
  100 - (
    avg by(instance) (
      rate(node_cpu_seconds_total{mode="idle"}[5m])
    ) * 100
  )
  ```
- 

  
