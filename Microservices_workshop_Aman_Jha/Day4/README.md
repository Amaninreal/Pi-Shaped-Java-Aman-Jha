# Microservices Resilience with Resilience4j

**Author:** Aman Jha  
**Tech Stack:** Spring Boot, Resilience4j, Spring Web, Spring Actuator

This project demonstrates how to build a resilient microservice using Resilience4j with Spring Boot.  
It includes patterns such as **Retry**, **TimeLimiter**, **Bulkhead**, and **Fallback** handling on a sample `/payment/process` endpoint.

---

## Project Structure

resilience4j-demo/
├── src/
│ └── main/
│ ├── java/com/example/payment/
│ │ ├── PaymentController.java
│ │ ├── PaymentService.java
│ │ └── Resilience4jDemoApplication.java
│ └── resources/
│ └── application.yml
└── README.md


---

## Objective

Implement and observe:

- ✅ Retry mechanism for failed requests
- 🕓 TimeLimiter for slow responses
- 🚧 Bulkhead for concurrency control
- 💥 Fallback mechanism to handle failures gracefully
- 📊 Actuator metrics for real-time monitoring

---

## Endpoint

### ➤ Process Payment API

```bash
GET /payment/process?fail=true|false&delay=true|false
```

| Param | Type    | Description                          |
|-------|---------|--------------------------------------|
| fail  | boolean | If true, the service throws an error |
| delay | boolean | If true, simulates 3s delay          |

---

## Resilience Patterns Used

### 1. Retry

- **Behavior:** Retries failed calls up to 3 times before failing
- **Trigger:**
    ```bash
    GET /payment/process?fail=true
    ```
  
### 2. TimeLimiter

- **Behavior:** Fails if processing exceeds 1 second
- **Trigger:**
    ```bash
    GET /payment/process?fail=false&delay=true
    ```

### 3. Bulkhead

- **Behavior:** Allows only 5 concurrent calls
- **Trigger:**  
```bash
  Run this in terminal:
```bash
for i in {1..10}; do
  curl "http://localhost:8080/payment/process?delay=true&fail=false" &
done
wait
```

## Fallback Message

If any resilience pattern is triggered:
    ```bash
    ❌ Payment service is currently unavailable. Please try again later.
    ```

## Resilience4j Configuration (application.yml)
```yaml
resilience4j:
  retry:
    instances:
      paymentServiceRetry:
        max-attempts: 3
        wait-duration: 500ms

  timelimiter:
    instances:
      paymentServiceTimeLimiter:
        timeout-duration: 1s

  bulkhead:
    instances:
      paymentServiceBulkhead:
        max-concurrent-calls: 5
        max-wait-duration: 0

management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics
```

## Monitoring with Actuator

Access actuator metrics:
```bash
http://localhost:8080/actuator/metrics
```

## Key metrics:

- resilience4j.retry.calls

- resilience4j.timelimiter.calls

- resilience4j.bulkhead.available.concurrent.calls

## Test Scenarios
Test	URL	Expected Result
Retry test	/payment/process?fail=true	Retries 3 times, then fallback triggered
Timeout test	/payment/process?delay=true	Fails due to delay > 1s timeout
Bulkhead test	10 parallel calls using shell loop	Some succeed, others fallback due to limit
Success test	/payment/process	✅ Payment processed successfully!

---

## Run the App
```bash
mvn spring-boot:run
```