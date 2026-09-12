
## What is Monitoring?
- Continuously checks system health and issuing alerts if a problem arises.
- Monitoring = Is something wrong?
- Monitoring is usually strong at detecting known problems.
  For example: You already know that:
  - High CPU usage is a problem.
  - A full disk is a problem.
  - An increase in 5xx errors is a problem.
  - Increased latency is a problem
 
## What is Observability?
- The capability to understand the system's internal behavior and identify the root cause of unknown problems using available system data.
- **Monitoring** indicates that there is a problem.
- **Observability** helps understand why there is a problem.

**Example:** 

- You received an alert: ```🚨 API latency > 2 seconds```. This is the **monitoring**.
- Now you investigate:
  ```
  API latency
     ↓
  Service A
     ↓
  Service B
     ↓
  Database
     ↓
  Slow SQL query
  ```
  And you discover: ```Database query = 4.8 seconds```. This is the **Observability**.

## Monitoring vs Observability

| Monitoring                       | Observability                                                  |
| -------------------------------- | -------------------------------------------------------------- |
| System health track karta hai    | System behavior understand karne mein help karta hai           |
| Known problems detect karta hai  | Known + unknown problems investigate karne mein help karta hai |
| Mostly predefined metrics/alerts | Metrics + Logs + Traces + context                              |
| "What is wrong?"                 | "Why is it wrong?"                                             |
| Alerting focused                 | Investigation + troubleshooting focused                        |
| Usually dashboards + alerts      | Telemetry + correlation + exploration                          |

#### Example
- Suppose users complain: "Website is very slow."
- Monitoring dashboard:
  ```
  CPU       = 40%
  Memory    = 50%
  HTTP 5xx  = 1%
  Latency   = 5 sec
  ```
- Monitoring indicated: Latency is high.
- You investigate further using Observability tools:
  ```
  Request
   ↓
  API
   ↓
  Payment Service
   ↓
  Database
   ↓
  Slow query
  ```
- And the trace revealed: ```DB query: 4.7 sec```
- Logs: ```Query timeout/retry```
- Now you have evidence to identify the root cause.

## The 3 Pillars of Observability
In traditional Observability there are 3 major telemetry signals:
```
              Observability
                   |
        ┌──────────┼──────────┐
        ↓          ↓          ↓
     Metrics      Logs      Traces
        |          |          |
     "How much?"  "What?"   "Where?"
```

### Metrics
- Numerical data.
```
CPU = 70%
Memory = 80%
Request rate = 500 req/sec
Error rate = 2%
Latency = 300 ms
```
- Common tool: Prometheus

### Logs
- Events of application or system.
```
2026-09-10 10:30:21 ERROR
Payment request failed
order_id=12345
timeout=5s
```
- Common tools:
  - Loki
  - Elasticsearch / OpenSearch
  - Fluent Bit
  - Logstash
 
### Traces
- A trace is the journey of a single request from start to finish as it travels through your entire system.
```
  User Request
    |
    ├── API Gateway       20ms
    |
    ├── User Service      50ms
    |
    ├── Payment Service   800ms
    |
    └── Database          700ms
```
- It appears that the bottleneck could be with the payment service or on the DB side.
- Common tools:
  - OpenTelemetry
  - Jaeger
  - Tempo

## Golden Signals
- The "4 Golden Signals" are commonly used in the Google SRE methodology.
  - Latency
  - Traffic
  - Errors
  - Saturation

  ### Latency
  - How long is it taking for the request to be completed?
  - Example: ```API response latency = 200 ms```
  - Looking only at average latency in production is not enough.
  - Better:
    ```
    P50 = 100 ms
    P95 = 400 ms
    P99 = 1.2 sec
    ```
  - Interview point: Latency should ideally be measured separately for successful and failed requests because errors can sometimes return very quickly.
    ```
    Successful request = 500 ms
    Failed request     = 20 ms
    ```
  - If you look only at average latency, the picture can be misleading.
   
  ### Traffic
  - How much load or how many requests is the system receiving?
  - Example: ```Request/sec = 1,000```
  - Other Examples:
    ```
    HTTP requests/sec
    Messages/sec
    Transactions/sec
    Active users
    Network packets/sec
    ```
  - Suppose normal traffic ```1,000 req/sec```. Suddenly ```10,000 req/sec```
  - So, The system might be overloaded.

  ### Errors
  - How many requests are failing?
  - Example:
    ```
    Total requests = 100,000
    Failed requests = 2,000

    Error rate = 2%
    ```
  - Errors include:
    ```
    HTTP 5xx
    HTTP 4xx (depending on use case)
    Timeouts
    Connection failures
    Application exceptions
    ```
    > Important: Not every 4xx necessarily system failure. Ex: ```404 Not Found``` ERROR could be wrong URL. But ```500 Internal Server Error``` usually indicate application/server-side issue.

  ### Saturation
  - How close are the system resources to their limits OR How busy your resources are.
  - Example:
    ```
    CPU utilization = 95%
    Memory = 90%
    Disk = 95%
    Connection pool = 98%
    Thread pool = 95%
    ```
  - Saturation basically asks "System ki capacity kitni consume ho chuki hai?"
    Example: ```DB connection pool: Maximum = 100 and current used = 98``` ```Saturation 98%```
  - If traffic increases further, requests may delays or fail.
      
### Golden Signals - quick summary
| Signal         | Simple question                   | Example     |
| -------------- | --------------------------------- | ----------- |
| **Latency**    | Request kitni slow hai?           | P99 = 2 sec |
| **Traffic**    | Kitna load aa raha hai?           | 5k req/sec  |
| **Errors**     | Kitni requests fail ho rahi hain? | 3% 5xx      |
| **Saturation** | Capacity kitni consume ho gayi?   | CPU 95%     |

> Ques: What are the four golden signals?
> 
> The four golden signals are latency, traffic, errors, and saturation. Latency tells us how long requests take, traffic tells us the demand on the system, errors tell us how many requests are failing, and saturation tells us how close the system is to its resource limits.

## RED Method
- Purpose of RED: "Meri service users ke perspective se kaisi perform kar rahi hai?"
- The RED method is useful for monitoring services/microservices.
  ```
  R -> Rate
  E -> Erros
  D -> Duration
  ```
  **R — Rate**
  - How many requests are coming in? ```Requests/sec = 2,000```
    Example:
    ```
    GET /users = 1,000 req/sec
    POST /orders = 500 req/sec
    GET /products = 500 req/sec
    ```
  **E — Errors**
  - How many requests are fails? ```Error rate = 2.5%```
    Example:
    ```
    HTTP 500 = 2%
    Timeouts = 0.5%
    ```

  **D — Duration**
  - How long are the requests taking to complete?
    ```
    P50 = 100ms
    P95 = 400ms
    P99 = 1s
    ```

## USE Method
- This method is useful for analyzing infrastructure and resources.
- This method is associated with Brendan Gregg and is commonly used in infrastructure performance analysis.
  ```
  U → Utilization
  S → Saturation
  E → Errors
  ```
  **U** — Utilization
  - How busy is the resource?
  - Example:
    ```
    CPU utilization = 85%
    Disk utilization = 70%
    ```

  **S** — Saturation
  - How much pending work does the resource have, or how close are they to their resource limit?
  - Example:
    ```
    CPU run queue high
    Disk I/O queue high
    Thread pool exhausted
    DB connection pool nearly full
    ```
  - Important: Utilization and saturation are not the same.
  - Example:
    ```
    CPU utilization = 70%
    CPU run queue = very high
    ```
  - CPU is 70% busy, but the request is waiting for the CPU. That's saturation.

  **E** — Errors
  - Resource-related errors.
  - Example:
    ```
    Disk I/O errors
    Network packet errors
    NIC errors
    Filesystem errors
    Hardware errors
    ```

## RED vs USE
| RED                      | USE                              |
| ------------------------ | -------------------------------- |
| Services ke liye         | Infrastructure/resources ke liye |
| Rate                     | Utilization                      |
| Errors                   | Saturation                       |
| Duration                 | Errors                           |
| User/request perspective | Resource perspective             |

**RED** = Request/Service side
**USE** = Underlying infrastructure side

Example:
```
              Application
                  |
             RED Method
          /      |       \
       Rate    Errors   Duration
                  |
                  ↓
            Infrastructure
                  |
             USE Method
          /      |       \
 Utilization  Saturation  Errors
```
    
### Quest: How would you monitor a production service?
- I would start with four golden signals — latency, traffic, errors, and saturation. For service-level monitoring, I would use the RED method to track request rate, errors, and duration. For infrastructure, I would use the USE method to monitor utilization, saturation, and errors. Along with metrics, I would collect logs and distributed traces so that when an alert fires, we can investigate the root cause rather than just knowing that something is wrong.

```
MONITORING
    ↓
Something is wrong?

OBSERVABILITY
    ↓
Why is it wrong?

GOLDEN SIGNALS
    ↓
Latency
Traffic
Errors
Saturation

RED
    ↓
Rate
Errors
Duration

USE
    ↓
Utilization
Saturation
Errors
```

## P50, P90, P95, P99

- A percentile basically indicates: "What percentage of requests were completed within this latency?"

Suppose you received 100 API requests.
```
Fastest                                      Slowest
|-----------------------------------------------|
1ms  2ms  5ms  10ms ... 100ms ... 500ms ... 5sec
```

**P50 — Median**
- P50 = 50% of requests were completed within this latency.
- Example: ``` P50 = 100 ms```.
- Meaning, 50% requests 100 ms ya usse kam mein complete hui.
- And roughly 50% 100 ms requests 100 ms se zyada le sakti hain.

**P90**
- ```P90 = 200 ms```
- Meaning: 90% requests 200 ms ya usse kam mein complete hui.
- Sirf 10% requests 200 ms se zyada slow thi.
  
**P95**
- ```P95 = 500 ms```
- Meaning: 95% requests 500 ms ya usse kam mein complete hui.
- Sirf 5% requests 500 ms se zyada slow thi.
 
**P99**
- ```P99 = 2 seconds```
- Meaning: 99% requests 2 seconds ya usse kam mein complete hui.
- Sirf 1% requests 2 seconds se zyada slow thi.    
         
| Percentile | Simple meaning                   |
| ---------- | -------------------------------- |
| **P50**    | 50% requests this fast or faster |
| **P90**    | 90% requests this fast or faster |
| **P95**    | 95% requests this fast or faster |
| **P99**    | 99% requests this fast or faster |

## Why P99 is important in SRE?
- Average latency: ```Average = 150ms```
- Looking at average latency you can say "The application is fast"
- But the actual picture could be:
  ```
  P50 = 100ms
  P95 = 500ms
  P99 = 5 seconds
  ```
- This means the majority of users find the application fast, but a small percentage are having a very poor experience. That's why in production monitoring P95/P99 are very useful.

**Interview one-liner:** P99 latency means 99% of requests complete within that latency, while the slowest 1% take longer.















