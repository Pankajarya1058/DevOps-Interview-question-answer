
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
