# EVENTRA — Assumptions & Constraints

---

## 1. Confirmed Facts vs. Documented Assumptions

> [!NOTE] Confirmed Facts
> 1. **Solo Developer Execution**: The system will be built, maintained, and tested entirely by a single developer.
> 2. **Task 1 Baseline**: Internship Task 1 requires a pure HTML5/CSS3 responsive landing page without heavy JavaScript framework dependencies.
> 3. **Architecture Constraint**: The backend must start as a clean Modular Monolith deployed in a single unit.
> 4. **No External Budget**: Development relies on open-source software, free-tier services, and local Docker emulation.

---

## 2. Structured Assumptions Table

### A. Business Assumptions
- **Assumption**: Event organizers prefer flat or low commission fees per ticket over complex custom contract pricing.
  - *Reason*: Target market is tech meetups, workshops, and mid-sized conferences.
  - *Impact if False*: Need to build multi-tiered billing models and contract negotiation tools.
  - *Validation*: Validate during Phase 2 user research surveys with meetup organizers.
- **Assumption**: 5-minute reservation hold timers strike the optimal balance between customer checkout completion and ticket inventory release.
  - *Reason*: Standard industry benchmark; long enough for payment input, short enough to prevent stock hoarding.
  - *Impact if False*: Excessive abandoned inventory holds or customer checkout drop-offs.
  - *Validation*: Validate via booking flow analytics in Phase 5.

### B. Technical Assumptions
- **Assumption**: A single PostgreSQL instance with atomic row locking and Redis TTL keys can handle flash-sale surges of up to 500 RPS without database deadlocks.
  - *Reason*: PostgreSQL row-level locks combined with Redis key expiration provide sub-millisecond concurrency protection.
  - *Impact if False*: Need to introduce an asynchronous queue (e.g. RabbitMQ/Kafka) for ticket reservations earlier.
  - *Validation*: Validate in Phase 6 via k6 load testing scripts.
- **Assumption**: HMAC-SHA256 encrypted QR codes are sufficient to prevent ticket duplication without requiring physical scanner hardware.
  - *Reason*: Cryptographic signatures verify payload authenticity instantly via software camera scanners.
  - *Impact if False*: Would require specialized scanner hardware or online-only live lookup.
  - *Validation*: Validate in Phase 6 gate scanner prototype testing.

### C. User Assumptions
- **Assumption**: Attendees will access ticket QR passes primarily on mobile smartphones at venue entry.
  - *Reason*: Over 85% of event check-in traffic occurs via mobile devices.
  - *Impact if False*: Need to optimize printable paper PDF pass layout and desktop flows.
  - *Validation*: Validate in Phase 3 UX testing across mobile viewports.
- **Assumption**: Gate staff will use their own iOS/Android smartphones with web browsers to perform ticket scanning.
  - *Reason*: Avoids requiring custom hardware procurement for event staff.
  - *Impact if False*: Need to build standalone native desktop/mobile apps.
  - *Validation*: Validate during Phase 6 gate scanner testing.

### D. Infrastructure Assumptions
- **Assumption**: Single-node containerized deployment (Docker Compose) on a modest Cloud VPS (e.g., 2 vCPU, 4GB RAM) is sufficient for initial production hosting.
  - *Reason*: Modular monolith efficiency eliminates microservice networking overhead.
  - *Impact if False*: Need to configure Kubernetes or multi-node cloud clusters prematurely.
  - *Validation*: Validate during Phase 7 deployment benchmarking.

### E. Development Assumptions
- **Assumption**: Code quality can be maintained solely through automated CI testing (Jest/Supertest/ESLint) and git pre-commit hooks.
  - *Reason*: Solo developer relies on automated tooling in lieu of peer code reviews.
  - *Impact if False*: Technical debt and regression bugs escape into main branch.
  - *Validation*: Continuous validation starting from Phase 4 backend development.

---

## 3. Explicit System Constraints

| Constraint Category | Constraint Specification | Architectural Implication |
| :--- | :--- | :--- |
| **6. Time Constraints** | Internship Task 1 UI baseline must be completed incrementally alongside Phase 1-3 planning. | Prioritize pure HTML5/CSS3 Grid/Flexbox implementation for v0.1 without getting blocked by backend setup. |
| **7. Budget Constraints** | \$0 external budget for paid cloud services, proprietary APIs, or enterprise software licenses. | Use open-source PostgreSQL, Redis, Docker, and self-hosted/free-tier tools (Stripe sandbox, SendGrid free tier). |
| **8. Solo-Developer Constraints** | All coding, architecture, testing, documentation, and deployment managed by one engineer. | Avoid complex distributed technologies (Kafka, Kubernetes, microservice meshes). Keep deployment model simple. |
| **9. Technology Constraints** | Monolith first; pure HTML5/CSS3 for Task 1; Node.js/TypeScript backend; SQL database for strict ACID transactions. | Relational database mandatory for financial/inventory ACID guarantees. No NoSQL for inventory core. |
| **10. Internship Constraints** | Code must fulfill Task 1 responsive landing page requirements while directly evolving into EVENTRA v0.1. | Task 1 code cannot be disposable. Must establish design tokens and reusable CSS structure for full platform. |

---

## 4. Definition of Done (DoD) — Sub-Phase 1.9
- [x] Confirmed facts explicitly separated from documented assumptions.
- [x] All 10 categories (Business, Technical, User, Infrastructure, Development, Time, Budget, Solo-Dev, Tech, Internship) addressed.
- [x] Each assumption details Statement, Reason, Impact if False, and Validation method.
- [x] Saved to [`docs/09_ASSUMPTIONS_AND_CONSTRAINTS.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/09_ASSUMPTIONS_AND_CONSTRAINTS.md).
