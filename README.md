# ScanOut

### Current Doc Version
v0.9 (MVP Stage)

---

## 1. Introduction

### Project name
**ScanOut**

### Brief description
ScanOut is a modular system for storing, visualizing, and analyzing geo and content data.  
The system combines a mobile and web client with different levels of access.

### Project goal
1) To create a universal platform for collecting, visualizing, and sharing data—particularly geo-data—that can scale from an MVP to a commercial version with a microservice architecture.  
2) Companies performing technical or construction work often face falsified photo reports, incorrect coordinates, and a lack of reliable proof of *who* actually completed a task *and where*. Traditional GPS trackers and CRMs cannot verify data authenticity.  
This system enables verification of location and work authenticity.

### Product history
This version is an MVP developed as a demo tool for coordinate visualization on a map (Leaflet + PostGIS).  
Unlike the company’s legacy product, the purpose of this MVP was to demonstrate architectural simplicity and the choice of TypeScript as the base language, capable—with minimal adjustments—of supporting web, mobile, and, if needed, desktop clients.  
Subsequent commercial versions were expanded into a full system with modules such as: Auth, Geo, Reader, Notifications, and others.

---

## 2. Overall Architecture

### Architectural approach
The MVP uses a **modular architecture** implemented within a single monorepo.  
The first full release is planned to transition to an **Event-Driven Microservices Architecture**.

### Core principles
- Object-Oriented structure (OOP approach instead of functional architecture)  
- Module independence (each responsible for its own domain)  
- Asynchronous event communication (via Kafka)  
- Scalability to microservices without breaking the API

### Modules (current MVP)
- **AuthModule** — user authentication and encryption  
- **GeoModule** — coordinate and geodata processing (PostGIS)  
- **ReaderModule** — visualization of text and media fragments  
- **NotificationModule** — notification system  
- **AdminModule** — management interface for data and users

---

## 3. Technology Stack

### Frontend
- Angular  
- Angular Material  
- Zustand (client state management)  
- Leaflet (maps)

### Backend
- Bun (REST API, WebSocket)  
- Node-compatible OOP-style modular code

### Messaging
- Kafka + Schema Registry (planned for microservice version)

### Databases
- PostgreSQL (authorization, system data)  
- MongoDB (content and documents)

### Deployment
- Docker Compose  
- **Google Cloud Run / Compute Engine**  
- Google Cloud Build (CI)

### Monitoring
- Google Cloud Logging  
- Prometheus + Grafana (planned)

---

## 4. Users and Roles

| Role         | Access Description |
|---------------|--------------------|
| **Admin**     | Full access to data, configurations, and user management |
| **Manager**   | Access to web client, analytics, and reports |
| **Engineer**  | Uses mobile client, access only to own data |

### Authentication & Authorization
- The web client uses standard password authentication (`bcrypt`).  
- The mobile client for engineers uses **username encryption embedded in an image** (steganographic encryption, **Steganographic Authentication**).  
- Identity verification occurs by extracting the encoded name and checking it with the server.  
- Token-based authentication (JWT + Refresh Tokens) will be implemented in the next stage.

---

## 5. Microservice / Module Descriptions

### AuthModule
- Handles authentication and unique steganographic encryption for mobile clients  
- Bcrypt password hashing for the web version  

### GeoModule
- Receives and records user coordinates  
- Integrated with PostGIS  
- Displays user routes and points on the map via Leaflet

### ReaderModule
- Displays text and multimedia materials  
- Search, indexing, and linking between content fragments

### NotificationModule
- Sends and receives notifications  
- Supports WebSocket messages and email triggers

### AdminModule
- Control panel for roles, users, and data  
- System parameter configuration

---

## 6. * Integration and Communication

### Data exchange mechanisms
- In the MVP, communication between modules occurs via REST endpoints  
- **Kafka topics** will be introduced later for asynchronous integration

### Versioning
- REST API: `/api/v1/...`  
- Future: `/api/v2` + versioned Kafka topics (`user.events.v2`)  
- Schema Registry for event validation  

### Scheduled tasks
- Cron processes for periodic data analysis and updates

---

## 7. Data and Storage

### General data model
- PostgreSQL → users, roles, authentication  
- MongoDB → content, fragments, events  
- PostGIS → coordinates, geometric objects

### Media and Files
- Images and media files stored in **Google Cloud Storage**  
- Each module has its own bucket  
- Caching — in-memory (per service) and LocalStorage on the frontend  

### Session and Progress
- User progress stored in MongoDB  
- Sessions validated via JWT tokens  

---

## 8. Security and PII

### Authentication Methods
- Web: bcrypt passwords  
- Mobile client: **steganographic username encryption** (Steganographic Authentication)  

### PII Isolation (Best Practices)
- Personal data stored in a separate database (in commercial versions)  
- Access only via PII-gateway  
- Access logs → Kafka topic `pii.access.log`  
- Full **GDPR compliance (EU regulations)**  

### Google Cloud Security Practices
- Secrets and keys — **Google Secret Manager**  
- Data encryption — **AES-256-GCM via Cloud KMS**  
- HTTPS certificates — **Google Managed SSL**  
- Identity and Access Management (IAM) for permissions separation  
- Audit Logs enabled for all GCP services  

---

## 9. Logging and Monitoring

### Application logs
- Google Cloud Logging  
- Console and centralized Bun server logs  

### Event logs
- Kafka (future implementation)  
- Archived in Google Cloud Storage  

### System logs
- Docker container output integrated with Cloud Logging  
- Metrics monitored via Prometheus + Grafana  

### Retention
- 30 days for dev  
- 180 days for prod  
- Old logs archived to GCS  

---

## 10. DevOps and CI/CD

### Infrastructure
- Containerization via Docker  
- CI/CD — **Google Cloud Build + GitHub Actions**  
- Image storage — **Artifact Registry (GCP)**  

### Environments
- `local` — development  
- `production` — GCP Cloud Run  
- `dev` and `staging` environments planned  

### Best Practices (GCP)
- Infrastructure-as-Code (Terraform or Pulumi)  
- Secrets → Google Secret Manager  
- Build → Cloud Build triggers on push  
- Deploy → Cloud Run service revisions  
- Monitoring → Cloud Console + Grafana dashboards  

---

## 11. Testing and Quality

### Current
- Unit tests using **Jest** (for business logic and API)  
- Data and coordinate validation  

### Planned
- Integration tests (Testcontainers, Supertest)  
- End-to-End tests (Playwright / Cypress)  
- Contract tests (Pact + Schema Registry)  

### Best Practices (GCP)
- CI pipeline: GitHub Actions → Cloud Build  
- Isolated test containers  
- Reports stored in **Cloud Storage**  
- Automatic execution on each merge  

---

## 12. Monitoring and Incident Response

### Metrics
- Latency, Throughput, API Errors, Consumer Lag  
- Future integration: Prometheus → Grafana  

### Alerts
- Grafana Alerts → Email / Slack  
- Automatic container restart on failure (Cloud Run restart policy)  

### Response
- Rollback via Cloud Build revisions  
- DevOps Slack notifications  

---

## 13. Roadmap and Future Development

1. Transition from modular to microservice architecture  
2. Add a dedicated PII service  
3. Integrate Kafka and event-driven model  
4. WebSocket-based reactions and notifications  
5. Extended analytics and AI-powered data interpretation  
6. Offline mode for mobile client  
7. Machine learning and recommendation features  
8. Introduce `staging` environment and auto-scaling via Cloud Run
