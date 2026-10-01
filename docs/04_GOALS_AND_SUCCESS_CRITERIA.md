# EVENTRA — Goals & Success Criteria

---

## 1. Product Goals

### MVP (v0.1 - v0.5)
- **Goal**: Enable full end-to-end event discovery, single-tier ticket reservation, and checkout flow.
  - *Measurable Success Criterion*: A user can discover an event, select a ticket, reserve it, and complete a simulated checkout in $< 3$ minutes.
- **Goal**: Provide event organizers with event creation and ticket stock management capabilities.
  - *Measurable Success Criterion*: An organizer can create an event with stock allocation in $< 2$ minutes.

### Advanced (v0.8)
- **Goal**: Support multi-tier pricing (Early Bird, GA, VIP) and automated QR ticket issuance upon payment confirmation.
  - *Measurable Success Criterion*: 100% of confirmed paid bookings automatically generate a valid digital QR pass within 2 seconds of payment webhook receipt.

### Production (v1.0)
- **Goal**: Deliver an organizer analytics dashboard with real-time revenue and entry scan metrics.
  - *Measurable Success Criterion*: Dashboard metrics update within 5 seconds of gate scans or ticket sales.

---

## 2. Engineering Goals

### MVP (v0.1 - v0.5)
- **Goal**: Establish a clean Modular Monolith architecture with strict domain boundaries (Catalog, Booking, Auth, Payment, Notification).
  - *Measurable Success Criterion*: Zero cross-domain database access; all inter-module calls use explicit service interfaces.

### Advanced (v0.8)
- **Goal**: Implement concurrency safety for flash-sale ticket reservations.
  - *Measurable Success Criterion*: 0% overbooking rate under a simulated flash-sale surge of 100 concurrent reservation requests for 10 remaining tickets.

### Production (v1.0)
- **Goal**: Achieve containerized single-command local and cloud deployment.
  - *Measurable Success Criterion*: `docker compose up` starts database, backend, cache, and frontend ready to receive traffic within 30 seconds.

---

## 3. User Experience (UX) Goals

### MVP (v0.1 - v0.5)
- **Goal**: Deliver a responsive, mobile-first public event catalog UI (Task 1 / v0.1).
  - *Measurable Success Criterion*: 100% pass rate on mobile, tablet, and desktop visual viewports ($320\text{px}$ to $1920\text{px}$) without horizontal scrollbars.

### Advanced (v0.8)
- **Goal**: Ensure WCAG 2.1 AA accessibility compliance across public flows.
  - *Measurable Success Criterion*: Lighthouse / AXE accessibility score $\ge 95/100$ on all public pages.

### Production (v1.0)
- **Goal**: Provide transparent hold-timer countdown feedback during booking.
  - *Measurable Success Criterion*: Real-time UI countdown timer accurately reflects Redis TTL state with $\pm 1\text{s}$ precision.

---

## 4. Security Goals

### MVP (v0.1 - v0.5)
- **Goal**: Secure user authentication and password storage.
  - *Measurable Success Criterion*: Argon2id / bcrypt hashing with salt; zero plain-text credentials stored.

### Advanced (v0.8)
- **Goal**: Secure payment integration compliance and payload validation.
  - *Measurable Success Criterion*: 100% verification of payment gateway webhook cryptographic signatures before processing booking transitions.

### Production (v1.0)
- **Goal**: Cryptographically signed QR ticket passes to prevent entry fraud.
  - *Measurable Success Criterion*: HMAC-SHA256 signature verification blocks 100% of altered or duplicated QR pass check-in attempts.

---

## 5. Performance Goals

### MVP (v0.1 - v0.5)
- **Goal**: Ensure fast public landing page rendering.
  - *Measurable Success Criterion*: Largest Contentful Paint (LCP) $< 1.5\text{s}$, First Contentful Paint (FCP) $< 0.8\text{s}$ on 4G network throttling.

### Advanced (v0.8)
- **Goal**: Fast ticket hold allocation API response time.
  - *Measurable Success Criterion*: API reservation endpoint response time $< 150\text{ms}$ (p95).

### Production (v1.0)
- **Goal**: High-throughput flash-sale capacity.
  - *Measurable Success Criterion*: Sustained throughput of $\ge 500\text{ RPS}$ with 0 DB deadlocks under k6 load test scripts.

---

## 6. Reliability Goals

### MVP (v0.1 - v0.5)
- **Goal**: Graceful input validation and error handling.
  - *Measurable Success Criterion*: 0 unhandled 500 server crashes on invalid/malformed HTTP request payloads.

### Advanced (v0.8)
- **Goal**: Payment state machine reconciliation idempotency.
  - *Measurable Success Criterion*: Duplicate payment webhook delivery results in exactly 1 ticket fulfillment event (100% idempotent).

### Production (v1.0)
- **Goal**: High availability and crash recovery.
  - *Measurable Success Criterion*: Automatic service health recovery via Docker restart policies in $< 10\text{s}$.

---

## 7. Developer-Quality Goals

### MVP (v0.1 - v0.5)
- **Goal**: High test coverage for domain business logic.
  - *Measurable Success Criterion*: $\ge 80\%$ unit test coverage on core domain services (Catalog, Booking).

### Advanced (v0.8)
- **Goal**: Automated CI test execution and linting.
  - *Measurable Success Criterion*: GitHub Actions workflow runs unit/integration tests and ESLint on every pull request within 2 minutes.

### Production (v1.0)
- **Goal**: Comprehensive Architecture Decision Records (ADRs).
  - *Measurable Success Criterion*: 100% of major structural decisions documented in `docs/adr/`.

---

## 8. Resume / Project Demonstration Goals

### MVP (v0.1 - v0.5)
- **Goal**: Showcase clean code structure, semantic HTML5, modern CSS Grid/Flexbox, and professional Git commit history.
  - *Measurable Success Criterion*: Clear, conventional commits (`feat:`, `fix:`, `docs:`) and crisp README documentation.

### Advanced (v0.8)
- **Goal**: Demonstrate deep backend engineering concepts (concurrency locks, state machines, webhooks).
  - *Measurable Success Criterion*: Runnable demonstration script simulating 50 concurrent booking requests to prove zero overselling.

### Production (v1.0)
- **Goal**: Portfolio-ready live deployment with automated load test reports.
  - *Measurable Success Criterion*: Published load testing report (Grafana / k6 export) proving 500+ RPS stability.

---

## 9. Definition of Done (DoD) — Sub-Phase 1.4
- [x] All 8 goal categories defined with concrete metrics.
- [x] Goals explicitly split across MVP, Advanced, and Production tiers.
- [x] Documentation saved to [`docs/04_GOALS_AND_SUCCESS_CRITERIA.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/04_GOALS_AND_SUCCESS_CRITERIA.md).
