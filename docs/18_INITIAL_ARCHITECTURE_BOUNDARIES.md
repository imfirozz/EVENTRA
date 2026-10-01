# EVENTRA — Initial Architecture Boundaries

---

## 1. Architectural Boundary Map

```
                  ┌──────────────────────────────────────────────────────────┐
                  │                 FRONTEND BOUNDARY                        │
                  │  Static HTML5/CSS3 (v0.1 UI Baseline) / SPA Components    │
                  └────────────────────────────┬─────────────────────────────┘
                                               │ (HTTPS / JSON REST API)
                                               ▼
┌───────────────────────────────────────────────────────────────────────────────────────────┐
│                                   BACKEND BOUNDARY                                        │
│                              (Modular Monolith API Unit)                                  │
│                                                                                           │
│  ┌────────────────┐   ┌────────────────┐   ┌────────────────┐   ┌──────────────────────┐  │
│  │ Catalog Module │   │ Auth Module    │   │ Booking Module │   │ Payment Module       │  │
│  └────────────────┘   └────────────────┘   └────────────────┘   └──────────────────────┘  │
│                                                                                           │
│  ┌─────────────────────────────────────┐   ┌───────────────────────────────────────────┐  │
│  │ Background Processing (BullMQ/Redis)│   │ Notification Module (Email Adapter)       │  │
│  └─────────────────────────────────────┘   └───────────────────────────────────────────┘  │
└───────────────┬──────────────────────────────────┬───────────────────────────┬────────────┘
                │ (Internal Network)               │ (Internal Network)        │ (HTTPS API)
                ▼                                  ▼                           ▼
  ┌───────────────────────────┐      ┌───────────────────────────┐   ┌─────────────────────┐
  │     DATABASE BOUNDARY     │      │   FILE / STORAGE BOUNDARY │   │  EXTERNAL SERVICES  │
  │ PostgreSQL (ACID Storage) │      │ Local / Object Media      │   │ Stripe / SendGrid   │
  └───────────────────────────┘      └───────────────────────────┘   └─────────────────────┘
```

---

## 2. Formal Architectural Boundary Specifications

### A. Frontend Boundary
- **Scope**: Serves user-facing interface assets (Task 1 responsive HTML5/CSS3 landing page for `v0.1.0`; SPA/React components for later phases).
- **Enforcement**: Communicates with the backend exclusively via public HTTPS REST endpoints. Zero direct database queries, zero server secret key exposure.

### B. Backend Boundary
- **Scope**: Single Node.js/TypeScript process executing all core business logic (Routing, Schema Validation, Auth Guards, Domain Services).
- **Enforcement**: Organized into clean domain module folders (`catalog`, `booking`, `auth`, `payment`, `notification`). Inter-module communication uses strongly-typed TypeScript function calls.

### C. Database Boundary
- **Scope**: Primary PostgreSQL relational database storing all domain entities (Users, Events, Ticket Tiers, Bookings, Audit Logs).
- **Enforcement**: Direct database connections restricted exclusively to backend repository layers. Business logic modules interact with their own tables; zero cross-domain raw SQL joins without explicit interface abstraction.

### D. External Service Boundary
- **Scope**: Third-party integrations (Stripe Payment Gateway, SendGrid Email Service).
- **Enforcement**: Wrapped behind isolated service adapter interfaces (`IPaymentAdapter`, `IEmailAdapter`). External failures or API breaking changes affect only the adapter layer.

### E. Authentication Boundary
- **Scope**: Manages user registration, credential verification, JWT token signing/verification, and RBAC permission checks.
- **Enforcement**: Protected API endpoints verify authentication headers before routing requests to downstream domain services.

### F. Payment Boundary
- **Scope**: Coordinates ticket reservation checkout with payment gateway webhooks.
- **Enforcement**: 100% PCI-DSS SAQ A compliance delegation to Stripe. Zero credit card numbers or sensitive financial tokens touch EVENTRA database tables.

### G. File / Storage Boundary
- **Scope**: Stores uploaded media assets (event banner images, organizer logos).
- **Enforcement**: Binary media files stored in filesystem/object storage; database stores only relative string URL paths (`/assets/images/banner1.jpg`). Zero raw BLOB storage in SQL database tables.

### H. Notification Boundary
- **Scope**: Handles dispatching transactional emails (booking confirmations, QR passes).
- **Enforcement**: Decoupled from HTTP request handlers via event listeners; receives domain events (e.g. `BookingPaidEvent`) and queues email tasks.

### I. Background Processing Boundary
- **Scope**: Manages asynchronous tasks (email delivery, Redis TTL hold cleanup, QR ticket PDF generation).
- **Enforcement**: Operates via in-process background worker queue (BullMQ + Redis) to ensure I/O tasks never block the primary HTTP thread.

---

## 3. Foundational Principle: MODULAR MONOLITH FIRST

> [!IMPORTANT] Core Architectural Mandate
> EVENTRA **MUST** be implemented as a **Modular Monolith** in a single codebase and single deployment container. 
> 
> **Why Modular Monolith?**
> 1. **Zero Microservice Overhead**: No distributed network latency, no complex RPC failure handling, no Kubernetes orchestration needed for a solo developer.
> 2. **Refactoring Velocity**: Module boundaries are enforced via TypeScript interfaces. Extracting a module into a microservice later requires zero code rewrites—only moving a directory behind an HTTP network listener.
> 3. **ACID Concurrency Guarantee**: Single PostgreSQL database transactions handle ticket inventory locks without distributed two-phase commit overhead.

---

## 4. Criteria for Future Service Extraction

A separate background worker process or microservice is **ONLY** justified if:

1. **CPU/Memory Starvation**: Background email PDF generation or QR image rendering causes CPU spikes that degrade HTTP API response times ($>200\text{ms}$).
   - *Action*: Extract BullMQ worker into a separate Docker container sharing the same codebase.
2. **Independent Gate Scan Traffic**: Physical venue gate entry scanner traffic (1,000 requests/minute) saturates the primary HTTP server.
   - *Action*: Spin up a dedicated read-only QR verification worker instance.

---

## 5. Architectural Decisions Deferred to Later Phases

To avoid premature design, the following technical details are explicitly **DEFERRED**:

| Deferred Architecture Decision | Target Phase | Rationale |
| :--- | :--- | :--- |
| **Exact DB Table Schemas & Foreign Keys** | **Phase 4 (Database Phase)** | Requires finalized product specifications from Phase 2. |
| **API Route Paths & JSON Payload Schemas** | **Phase 4 (Backend Phase)** | Requires REST API design contracts from Phase 2. |
| **Stripe Webhook Secret Rotation Mechanism** | **Phase 5 (Payments Phase)** | Requires payment gateway sandbox setup. |
| **Production Kubernetes / VPS Topology** | **Phase 7 (Deployment Phase)** | Focus on local Docker Compose setup first. |

---

## 6. Definition of Done (DoD) — Sub-Phase 1.18
- [x] All 9 architectural boundaries explicitly defined.
- [x] "Modular Monolith First" principle formally confirmed.
- [x] Conditions for future worker/service extraction documented.
- [x] List of deferred architectural decisions established.
- [x] Saved to [`docs/18_INITIAL_ARCHITECTURE_BOUNDARIES.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/18_INITIAL_ARCHITECTURE_BOUNDARIES.md).
