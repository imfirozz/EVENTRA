# EVENTRA — Master Milestones & Roadmap

---

## 1. Master Milestone Timeline

```
M0: Initiation ──> M1: Task 1 UI ──> M2: Requirements ──> M3: UX/UI Design ──> M4: Frontend
                                                                                   │
M9: Booking ──> M8: Events ──> M7: Auth ──> M6: Database ──> M5: Backend Monolith ◄┘
   │
   └──> M10: Payments ──> M11: Tickets/QR ──> M12: Admin ──> M13: Testing ──> M14: Deployment ──> M15: Performance ──> M16: Launch
```

---

## 2. Milestone Specifications

---

### M0 — Initiation (Phase 1)
- **Objective**: Establish foundational scope, vision, feasibility, risks, governance, and initial architecture boundaries.
- **Major Deliverables**: 21 Phase 1 documentation artifacts (`docs/01_...` to `docs/21_...`), `ADR-001`, repository directory tree.
- **Dependencies**: None.
- **Exit Criteria**: All Phase 1 sub-phases completed and locked in.

---

### M1 — Internship Landing Page / EVENTRA v0.1 (Task 1 Baseline)
- **Objective**: Build a responsive HTML5/CSS3 event catalog landing page using Flexbox, Grid, and media queries without JS frameworks.
- **Major Deliverables**: `src/v0.1-landing/index.html`, `css/styles.css`, `css/responsive.css`, accessible semantic markup.
- **Dependencies**: M0 Initiation.
- **Exit Criteria**: Passes $320\text{px}$, $768\text{px}$, and $1200\text{px}$ viewport checks without horizontal scrollbars; WCAG 2.1 AA compliant.

---

### M2 — Requirements Architecture (Phase 2)
- **Objective**: Define formal System Requirements Specifications (SRS) for functional and non-functional requirements.
- **Major Deliverables**: `SRS_FUNCTIONAL.md`, `SRS_NON_FUNCTIONAL.md`, domain data contracts.
- **Dependencies**: M0, M1.
- **Exit Criteria**: 100% of user personas and user journeys backed by detailed functional requirement specifications.

---

### M3 — UX/UI Design Architecture
- **Objective**: Establish design system guidelines, color tokens, typography scales, wireframe flows, and UI component specifications.
- **Major Deliverables**: Design System Guide, Component Hierarchy Map, Mobile/Desktop Layout Wireframes.
- **Dependencies**: M2.
- **Exit Criteria**: Complete UI design tokens and component specs documented.

---

### M4 — Frontend Foundation Architecture
- **Objective**: Build modern SPA frontend framework foundation (React/Vite) integrating v0.1 design tokens.
- **Major Deliverables**: SPA repository initialized, router setup, state management, reusable UI component library.
- **Dependencies**: M1, M3.
- **Exit Criteria**: Public catalog SPA views render cleanly with mock data fixtures; zero lint errors.

---

### M5 — Backend Foundation Architecture
- **Objective**: Initialize Node.js/TypeScript Modular Monolith API foundation with Express/NestJS, middleware, and domain module folders.
- **Major Deliverables**: Monolith API structure, global error handler, request logger, Zod schema validation middleware.
- **Dependencies**: M0, M2.
- **Exit Criteria**: Health check API endpoint returns 200 OK; TypeScript compiles under `strict: true`.

---

### M6 — Database Schema & ORM Architecture
- **Objective**: Design relational PostgreSQL schema with ACID transaction constraints and ORM migrations.
- **Major Deliverables**: Database ERD diagram, SQL migration scripts (`up`/`down`), seed data generator script.
- **Dependencies**: M5.
- **Exit Criteria**: Database migrations execute cleanly; relational constraints verified.

---

### M7 — Authentication & User Security Domain
- **Objective**: Implement secure user registration, Argon2id password hashing, JWT/Session authentication, and RBAC role guards.
- **Major Deliverables**: Auth domain module, login/register endpoints, JWT auth middleware, role guards (`ATTENDEE`, `ORGANIZER`).
- **Dependencies**: M6.
- **Exit Criteria**: User authentication unit/integration tests pass with $\ge 90\%$ coverage; password hashes secure.

---

### M8 — Event Management Domain
- **Objective**: Build event creation, editing, category filtering, and ticket tier inventory allocation APIs for organizers.
- **Major Deliverables**: Catalog domain module, Event CRUD endpoints, ticket tier inventory configuration service.
- **Dependencies**: M7.
- **Exit Criteria**: Organizers can create events and configure ticket stock via API; catalog keyword search verified.

---

### M9 — Booking Engine & Concurrency Control
- **Objective**: Build atomic ticket reservation engine with 5-minute Redis TTL locks and DB row locking to prevent overselling.
- **Major Deliverables**: Booking domain module, ticket reservation API (`/api/bookings/reserve`), Redis TTL lock manager, hold release worker.
- **Dependencies**: M8.
- **Exit Criteria**: Concurrent test suite proves 0% overbooking rate under 100 simultaneous requests for 10 tickets.

---

### M10 — Payment Integration & Webhook State Machine
- **Objective**: Integrate Stripe payment gateway checkout and idempotent webhook processing for booking payment reconciliation.
- **Major Deliverables**: Payment domain module, Stripe Checkout session generator, Stripe Webhook signature verifier (`Stripe-Signature`), payment state machine (`RESERVED -> PAID`).
- **Dependencies**: M9.
- **Exit Criteria**: 100% of verified payment webhooks transition ticket state to `PAID`; duplicate webhooks processed idempotently.

---

### M11 — Digital QR Tickets & Gate Validation Domain
- **Objective**: Generate cryptographically signed HMAC-SHA256 digital QR passes upon paid booking and build mobile camera gate scanner web app.
- **Major Deliverables**: Ticket QR generation module, HMAC signing service, Gate Scanner Web App view, check-in validation endpoint (`PAID -> CHECKED_IN`).
- **Dependencies**: M10.
- **Exit Criteria**: Mobile camera scanner validates valid QR pass in $<1$ second; duplicate scans blocked with "ALREADY USED" red screen.

---

### M12 — Organizer Analytics & Super-Admin Moderation
- **Objective**: Deliver organizer sales/revenue dashboards and super-admin platform moderation console.
- **Major Deliverables**: Analytics domain module, Super-Admin moderation endpoints, immutable audit logging system.
- **Dependencies**: M8, M11.
- **Exit Criteria**: Organizer dashboard displays real-time ticket sales count and revenue graphs; admin audit trail logs moderation actions.

---

### M13 — Testing & Security Hardening
- **Objective**: Comprehensive unit, integration, and security testing (Snyk dependency scanning, OWASP ZAP, PII audit).
- **Major Deliverables**: Integration test suite, security audit report, automated CI quality pipeline.
- **Dependencies**: M7 through M12.
- **Exit Criteria**: Overall unit test coverage $\ge 80\%$; zero high-severity security vulnerabilities.

---

### M14 — Containerized Deployment & Infrastructure
- **Objective**: Package platform into production-ready Docker Compose containers behind Nginx reverse proxy with SSL certificate termination.
- **Major Deliverables**: Production Dockerfile, `docker-compose.yml`, Nginx SSL configuration, VPS deployment guide.
- **Dependencies**: M13.
- **Exit Criteria**: Single-command deployment (`docker compose up`) launches database, cache, backend API, and frontend on VPS in $<30$ seconds.

---

### M15 — Observability & Performance Load Testing
- **Objective**: Instrument platform with Prometheus metrics, Grafana dashboards, Pino structured logging, and k6 load testing scripts.
- **Major Deliverables**: Telemetry metrics exporter, Grafana dashboard configs, k6 flash-sale load testing scripts.
- **Dependencies**: M14.
- **Exit Criteria**: System maintains $\ge 500\text{ RPS}$ throughput under k6 load test script with zero DB deadlocks and p95 latency $<200\text{ms}$.

---

### M16 — Production Hardening & Portfolio Launch
- **Objective**: Final production hardening, offsite backup setup, domain SSL lockdown, and portfolio demonstration write-up.
- **Major Deliverables**: Live production site URL, published k6 load test report, portfolio case study documentation.
- **Dependencies**: M15.
- **Exit Criteria**: Fully functional live deployment ready for demonstration with complete SDLC documentation.

---

## 3. Definition of Done (DoD) — Sub-Phase 1.20
- [x] All 17 master milestones (M0 through M16) defined with Objective, Major Deliverables, Dependencies, and Exit Criteria.
- [x] Master milestone timeline diagram created.
- [x] Saved to [`docs/20_MILESTONES_AND_ROADMAP.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/20_MILESTONES_AND_ROADMAP.md).
