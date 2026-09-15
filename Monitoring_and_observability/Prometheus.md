
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

  ###

  
  
