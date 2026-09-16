
## 1. What is Grafana?
- Grafana is a visualization and observability platform used to visualize and analyze metrics, logs, and traces in dashboards.
- Simple architecture:
  ```
  Linux Server
     ↓
  Node Exporter
     ↓
  Prometheus
     ↓
   Metrics
     ↓
  Grafana
     ↓
  Dashboard
  ```
- Grafana dashboard example:
  ```
  ┌─────────────────────────────────────┐
  │        Production Overview          │
  ├────────────┬───────────┬────────────┤
  │ CPU        │ Memory    │ Disk       │
  │ 72%        │ 68%       │ 75%        │
  ├────────────┴───────────┴────────────┤
  │ Request Rate                        │
  │  █████████████████  2.5k req/s      │
  ├─────────────────────────────────────┤
  │ Error Rate                          │
  │  1.2%                               │
  ├─────────────────────────────────────┤
  │ P99 Latency                         │
  │  450 ms                             │
  └─────────────────────────────────────┘
  ```

## 2. Datasources = Grafana ko data kahan se milega.
- Grafana itself does not normally collect metrics.
- It requires data from a data source.
- Examples:
  ```
  Prometheus
  Loki
  Elasticsearch/OpenSearch
  InfluxDB
  Jaeger
  Tempo
  MySQL
  PostgreSQL
  ```
- We configure Prometheus URL in Grafana ```http://prometheus-server:9090```
- Grafana then queries data from Prometheus.

**Ques: Does Grafana store metrics?**
- Grafana primarily acts as a visualization and observability interface. In a typical Prometheus setup, Prometheus stores the metrics and Grafana queries Prometheus to visualize them.

## 3. Dashboards = multiple visualizations ko ek screen par organize karna.
- Purpose: ```Viewing system health and important metrics in a central location.```
- Example:
  ```
  Production Kubernetes Dashboard

  ┌─────────────────────────────────────┐
  │ Cluster Health                      │
  ├──────────┬──────────┬───────────────┤
  │ Nodes    │ Pods     │ Restarts      │
  │ 10       │ 150      │ 3             │
  ├──────────┴──────────┴───────────────┤
  │ CPU Usage                           │
  │       /\    /\                      │
  │  ___ /  \__/  \___                  │
  ├─────────────────────────────────────┤
  │ Memory Usage                        │
  │       ______                        │
  │  ____/      \_____                  │
  ├─────────────────────────────────────┤
  │ Error Rate / Latency                │
  └─────────────────────────────────────┘
  ```

## 4. Panels = dashboard ke andar ek individual visualization.
- Example:
  ```
  Dashboard
    |
    ├── CPU Panel
    ├── Memory Panel
    ├── Disk Panel
    ├── Network Panel
    └── Error Rate Panel
  ```
- Can be different types of panels:
  ```
  - Time series
  - Stat
  - Gauge
  - Table
  - Bar chart
  - Heatmap
  - Logs
  ```

**Dashboard** = whole page.
**Panel** = page ke andar individual visualization.

## 5. PromQL queries in Grafana.
- In Grafana's Prometheus data sources, we retrieve data using PromQL.
- Example: ```up```. It will show targets status ```1 = UP``` OR ```0 = DOWN```

  **CPU Panel**
  - Example:
    ```
    # You can use this query in a Grafana time-series panel.
    
    100 - (
      avg by(instance) (
        rate(node_cpu_seconds_total{mode="idle"}[5m])
      ) * 100
    )
    ```
  - Output:
    ```
    Server 1 → 40%
    Server 2 → 65%
    Server 3 → 82%
    ```

  **Memory Panel**
  - Example:
    ```
    100 * (
      1 -
      node_memory_MemAvailable_bytes
      /
      node_memory_MemTotal_bytes
    )
    ```
  - Result: ```Memory Usage = 72%```
 
  **Disk Usage**
  - Conceptually:
    ```
    100 * (
      1 -
      node_filesystem_avail_bytes
      /
      node_filesystem_size_bytes
    )
    ```
  - Result:
    ```
    /     → 75%
    /var  → 82%
    /home → 45%
    ```

## 6. Variables
- Suppose you have 100 servers.
- It would be very inconvenient, If you need to manually select the server in the dashboard.
- You can create variable. ```Instance: [server1 ▼]```
- User can select from dropdown.
  ```
  Instance
     ↓
  server1
  server2
  server3
  server4
  ```
- Dashboard will show data of dynamically selected server.
- Example:
  ```
  100 - (
    avg by(instance) (
      rate(node_cpu_seconds_total{
        instance="$instance",
        mode="idle"
      }[5m])
    ) * 100
  )
  ```
- Here ```$instance``` is a Grafana variable.
- If user select ```server1:9090```. so the query will effectively be filtered for that server.

## 7. Multiple Variables
- A production dashboard can have multiple variables.
  ```
  Environment: [prod ▼]

  Cluster: [production-cluster ▼]

  Namespace: [default ▼]

  Service: [payment ▼]

  Instance: [server1 ▼]
  ```
- This dashboard can now be used for multiple environments/services.
- It reduces dashboard duplication.
  
## 8. How will you design the production dashboard?
- From an SRE perspective, a dashboard shouldn't just show CPU and memory. User impact and reliability metrics are also important.
- A useful production application dashboard:
  ```
  ┌──────────────────────────────────────────┐
  │          APPLICATION OVERVIEW            │
  ├────────────┬────────────┬────────────────┤
  │ Traffic    │ Errors     │ P95/P99        │
  │ 2k req/s   │ 1.2%       │ 450ms / 900ms  │
  ├────────────┴────────────┴────────────────┤
  │ Request Rate                             │
  │                  /\                      │
  │       __________/  \_____                │
  ├──────────────────────────────────────────┤
  │ Error Rate                               │
  │                         /\               │
  │ _______________________/  \___           │
  ├──────────────────────────────────────────┤
  │ Latency P50 / P95 / P99                  │
  ├──────────────────────────────────────────┤
  │ Service Health                           │
  ├──────────────────────────────────────────┤
  │ CPU / Memory / Disk / Network            │
  └──────────────────────────────────────────┘
  ```

## 9. Infrastructure Dashboard
- For the Linux Infrastructure:
  ```
  CPU
  ├── CPU Usage
  ├── Load Average
  └── CPU Saturation

  Memory
  ├── Used
  ├── Available
  └── Swap

  Disk
  ├── Filesystem Usage
  ├── Disk IOPS
  ├── Read/Write
  └── I/O Wait

  Network
  ├── Receive
  ├── Transmit
  ├── Errors
  └── Drops
  ```

## 10. Kubernetes Production Dashboard
```
Cluster
├── Nodes
├── Node CPU
├── Node Memory
└── Node Disk

Workloads
├── Deployments
├── Pods
├── Restarts
└── Replica availability

Application
├── Request Rate
├── Errors
└── Latency

Kubernetes
├── API Server
├── Scheduler
└── Controller Manager
```

### Interview Questions

#### Q1. What is Grafana?
- Grafana is a visualization and observability platform used to query and visualize metrics, logs and traces from different data sources.

#### Q2. What is a datasource?
- A datasource is the backend from which Grafana retrieves data, such as Prometheus, Loki, Elasticsearch or a database.

#### Q3. What is a dashboard?
- A dashboard is a collection of panels used to visualize and monitor system or application metrics.

#### Q4. What is a panel?
- A panel is an individual visualization inside a Grafana dashboard, such as a time-series graph, stat, gauge or table.

#### Q5. How does Grafana get data from Prometheus?
- Grafana is configured with Prometheus as a datasource. When a dashboard loads, Grafana sends PromQL queries to Prometheus, receives the time-series data and visualizes it in panels.

#### Q6. What are Grafana variables?
- Variables make dashboards dynamic. For example, we can create an instance or namespace variable so users can select different servers or Kubernetes namespaces without creating separate dashboards.

##### Q7. How would you design a production Grafana dashboard?
- I would start with user-impact and SRE metrics such as traffic, error rate, latency percentiles and saturation. Then I would add infrastructure metrics like CPU, memory, disk and network. I would use variables for environment, cluster, namespace, service and instance so the dashboard can be reused. I would also organize panels according to the troubleshooting flow, from high-level service health to detailed infrastructure metrics.
