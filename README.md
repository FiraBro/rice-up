# RICE: Real-time Insurance Claims Engine 🌾

[![Go Version](https://img.shields.io/badge/Go-1.23+-00ADD8?style=flat&logo=go)](https://go.dev/)
[![Node Version](https://img.shields.io/badge/Node.js-22-339933?style=flat&logo=node.js)](https://nodejs.org/)
[![Infrastructure](https://img.shields.io/badge/Infrastructure-Docker--Compose-blue?logo=docker)](https://www.docker.com/)

**RICE** is an enterprise-grade, event-driven microservices platform built to demonstrate high-throughput claim processing and real-time observability. It solves the "blocking" problem in legacy insurance systems by using a decoupled, asynchronous architecture.

---

## 🏗 System Architecture

The platform is built on the **Decoupled Service Pattern** to ensure independent scalability and fault tolerance.



* **Claims API (Go):** The high-performance entry point. Responsible for request validation, JWT-based RBAC, and persisting the "Source of Truth" to PostgreSQL.
* **Fraud Worker (Go):** An isolated consumer that processes high-load risk analysis without impacting API latency.
* **Notifier (Node.js):** A lightweight service specialized in maintaining long-lived WebSocket connections for sub-millisecond status updates.
* **Infrastructure:** Uses **Redis Streams** for durable event queuing and **PostgreSQL** for relational data with multi-tenant row isolation.

---

## 🚀 Senior-Level Features

### 1. Resilience & Reliability
* **Outbox Pattern:** Guaranteed consistency between Database state and Event messages to prevent data loss.
* **Graceful Shutdown:** All services handle `SIGTERM` to drain active tasks before exiting, ensuring zero-downtime deployments.
* **Exponential Backoff:** Intelligent retry logic for database and message broker reconnections during network partitions.

### 2. Performance & Scalability
* **Distributed Rate Limiting:** Global rate limiting enforced via Redis Fixed Window to prevent DDoS and API abuse.
* **Database Optimization:** Strategic use of GIN indexes for full-text search and optimized connection pooling (max open/idle connections).
* **Zero-Allocation Focus:** Optimized Go structs and the use of `sync.Pool` to reduce Garbage Collection (GC) pressure under high load.

### 3. Security & Governance
* **Identity:** JWT Authentication with RS256 (Asymmetric keys) signing for secure cross-service trust.
* **AuthZ:** Strict **Role-Based Access Control (RBAC)** separating PolicyHolders (Write-only) from Adjusters (Read/Write).
* **Multi-Tenancy:** Automated `tenant_id` injection at the middleware layer to prevent cross-tenant data leakage.

---

## 🛠 Tech Stack

| Layer | Technology |
| :--- | :--- |
| **Languages** | Go 1.23 (Core Logic), Node.js 22 (WebSockets) |
| **Frameworks** | Gin-Gonic (Go), Socket.io (Node), GORM (ORM) |
| **Database** | PostgreSQL (Relational), Redis (Cache/Broker) |
| **Testing** | k6 (Load Testing), Testcontainers (Integration) |
| **DevOps** | Docker, GitHub Actions (CI/CD), Prometheus |

---

## 📂 Project Structure

```text
.
├── services/
│   ├── claims-api/    # High-throughput REST API (Go)
│   ├── notifier/      # WebSocket Notification Engine (Node.js)
│   └── fraud-worker/  # Async Event Consumer (Go)
├── internal/          # Private shared packages (Auth, DB, Logging)
├── deployments/       # Production Dockerfiles & K8s manifests
└── scripts/           # Load testing (k6) & Seed scripts

🚥 Getting Started
1. Requirements
Docker & Docker Compose

Go 1.23+

2. Execution

# Clone and spin up the entire cluster
git clone [https://github.com/your-username/rice-platform.git](https://github.com/your-username/rice-platform.git)
cd rice-platform
docker-compose up --build

3. Verification
API Health: GET http://localhost:8080/health

Submit Claim: POST http://localhost:8080/api/v1/claims

📊 Observability & Metrics
The system exposes a /metrics endpoint for Prometheus to scrape.

P99 Latency Tracking: Monitor API response times under load.

Throughput Metrics: Track claims_processed_total across different tenants.

Health Probes: Liveness and Readiness endpoints integrated for Kubernetes.

🎙️ Interview Talking Points
Why Redis Streams over Kafka? For this specific scale, Redis Streams provides the necessary persistence and consumer group logic with significantly lower operational overhead.

Handling Race Conditions: Implementation of distributed locks in Redis to prevent multiple workers from processing the same claim twice.

Why Go? Go's memory footprint and goroutine model make it ideal for the high-concurrency requirements of a financial claims gateway.

---

### 🏁 Final Steps for Success:
1.  **System Design Image:** Use a tool like **Excalidraw** to create a simple diagram of the architecture, save it as `architecture.png`, and place it in your repo so the image tag in the README works.
2.  **License:** Create a `LICENSE` file with the MIT license text.
3.  **Go Workspaces:** Since you have multiple services, ensure you have a `go.work` file or a root `go.mod` to help your IDE (VS Code) understand the imports.
