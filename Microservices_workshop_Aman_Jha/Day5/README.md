# Observability Stack for Spring Boot Microservices - Day 5

Author: Aman Jha  

## Stack Overview

This project sets up an **observability stack** for microservices using Docker Compose. It includes:

- **Elasticsearch**: Log storage and search engine
- **Logstash**: Log processing pipeline
- **Kibana**: Visualization for logs
- **Prometheus**: Metrics collection
- **Grafana**: Metrics dashboards
- **Zipkin**: Distributed tracing
- **Spring Cloud Sleuth**: Trace instrumentation for Spring Boot

---

## 🐳 Docker Services

| Service       | Port  | Description                     |
|---------------|-------|---------------------------------|
| Elasticsearch | 9200  | Log storage                     |
| Kibana        | 5601  | UI for Elasticsearch logs       |
| Logstash      | 5000  | Parses and forwards logs        |
| Prometheus    | 9090  | Collects metrics                |
| Grafana       | 3000  | Visualizes Prometheus metrics   |
| Zipkin        | 9411  | Trace visualization             |

---

## Getting Started

### 1. Clone the Repository
```bash
git clone <repo-url>
cd Day5/observability
```

### 2. Start All Containers
```bash
docker-compose up -d
```

### 3. Verify Services
#### Elasticsearch
```bash
    http://localhost:9200
```

- Expected Output:
```bash
{
  "cluster_name": "docker-cluster",
  "tagline": "You Know, for Search"
}
```

#### Kibana
```bash
    http://localhost:5601
    Use it to view Elasticsearch logs.
```

#### Logstash

    Reads from logstash.conf and forwards to Elasticsearch.

#### Prometheus
```bash

    http://localhost:9090
    Go to Status > Targets to verify Spring Boot app is UP.
```

#### Grafana
```bash
    http://localhost:3000
```

- Default login:
```yaml
    Username: admin
    Password: admin
```

####  Zipkin
```bash
    http://localhost:9411
    Click Run Query to view traces.
```
---

## Spring Boot Configuration

Add the following dependencies in your Spring Boot app:

```bash

<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-registry-prometheus</artifactId>
</dependency>
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```
---

```yaml
Add to application.yml

management:
  endpoints:
    web:
      exposure:
        include: "*"
  metrics:
    export:
      prometheus:
        enabled: true
  endpoint:
    prometheus:
      enabled: true

spring:
  zipkin:
    base-url: http://localhost:9411
  sleuth:
    sampler:
      probability: 1.0

```
---

## Grafana Dashboard Setup

- Open Grafana → http://localhost:3000
```bash

    Login with admin/admin

    Add Prometheus as a Data Source

        URL: http://prometheus:9090
```
---

## Tracing with Zipkin

Trigger a request to your service:
```bash

curl http://localhost:8080/api/hello

```

Then go to Zipkin → Click Run Query
➡️ You’ll see the trace with timing and microservice path.

---

## Tear Down
```bash

docker-compose down
```





