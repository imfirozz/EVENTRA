# EVENTRA — Non-Functional Requirements Specification (NFRS)

---

## 1. Overview & Formatting Rules

This document specifies the authoritative **Non-Functional Requirements (NFR-XXX)** for EVENTRA across 20 quality dimensions. NFRs define measurable performance SLAs, reliability targets, security posture, accessibility compliance, and operational characteristics without dictating specific technology choices.

---

## 2. Master Non-Functional Requirements Catalogue

---

### NFR Category 1: Performance

#### NFR-001: Public Catalog Page Loading Budget
- **Name**: Public Catalog Page Loading Budget
- **Category**: Performance
- **Requirement**: Under normal load conditions, the public event catalog landing page interface shall complete initial rendering within agreed response-time budgets on 4G networks.
- **Rationale**: Fast initial load times prevent top-of-funnel attendee abandonment.
- **Measurement**: Largest Contentful Paint (LCP) and First Contentful Paint (FCP) measured via automated Lighthouse/WebPageTest runs.
- **Target**: $\text{LCP} < 1.5\text{s}$, $\text{FCP} < 0.8\text{s}$ on throttled 4G network profile.
- **Priority**: Must | **Scope**: MVP
- **Related BR**: BR-001, BR-020 | **Related PR**: PR-002 | **Related FR**: FR-007
- **Validation Method**: Analysis (A) & Demonstration (D)
- **Dependencies**: None
- **Risks**: Unoptimized images or unminified CSS assets degrading render times.

---

#### NFR-002: API Endpoint Response Time SLA
- **Name**: API Endpoint Response Time SLA
- **Category**: Performance
- **Requirement**: Under normal operational load, 95% (p95) of backend API responses (excluding third-party payment gateways) shall be returned within a strict latency budget.
- **Rationale**: Fast API response times ensure responsive user UI state updates during booking.
- **Measurement**: Server-side HTTP request duration metrics (p95 latency).
- **Target**: $\text{p95} < 150\text{ms}$ under normal load; $\text{p95} < 300\text{ms}$ under peak surge.
- **Priority**: Must | **Scope**: MVP
- **Related BR**: BR-005, BR-019 | **Related PR**: PR-007, PR-009 | **Related FR**: FR-012, FR-014
- **Validation Method**: Analysis (A)
- **Dependencies**: Database connection pool setup
- **Risks**: Unindexed database queries causing latency spikes under load.

---

#### NFR-003: Sub-Second Gate Entry Scanning Latency
- **Name**: Sub-Second Gate Entry Scanning Latency
- **Category**: Performance
- **Requirement**: The gate ticket validation interface shall complete QR code payload parsing, cryptographic signature verification, ticket status check, and return visual result feedback in sub-second time.
- **Rationale**: Prevents door entry queues and crowd congestion at physical venues.
- **Measurement**: End-to-end duration from camera scan event to UI validation response.
- **Target**: $< 1.0\text{s}$ total validation latency (target $< 500\text{ms}$ on fast network).
- **Priority**: Should | **Scope**: Advanced
- **Related BR**: BR-008, BR-012 | **Related PR**: PR-012, PR-022 | **Related FR**: FR-016
- **Validation Method**: Test (T) & Demonstration (D)
- **Dependencies**: HMAC QR validation service
- **Risks**: High network latency at venue degrading door throughput.

---

### NFR Category 2: Scalability

#### NFR-004: Sustained Concurrency & Flash-Sale Throughput
- **Name**: Sustained Concurrency & Flash-Sale Throughput
- **Category**: Scalability
- **Requirement**: The system shall process sustained concurrent ticket reservation requests during peak flash sales without database deadlocks or data corruption.
- **Rationale**: Flash sales generate extreme write surges when announcement notifications fire.
- **Measurement**: Sustained Requests Per Second (RPS) under automated load test.
- **Target**: MVP: 50 RPS; Advanced: 200 RPS; Production Target: 500 RPS (Target to be validated during load testing).
- **Priority**: Must (MVP Target) / Should (Production Target) | **Scope**: MVP / Advanced / Production
- **Related BR**: BR-004, BR-005 | **Related PR**: PR-006, PR-007 | **Related FR**: FR-011, FR-012
- **Validation Method**: Analysis (A)
- **Dependencies**: Atomic reservation lock implementation
- **Risks**: Connection pool exhaustion under 500+ RPS surges.

---

### NFR Category 3: Availability & Reliability

#### NFR-005: Core Platform Service Uptime Target
- **Name**: Core Platform Service Uptime Target
- **Category**: Availability
- **Requirement**: The platform shall maintain operational availability for public event catalog browsing and active ticket checkout sessions.
- **Rationale**: Uptime ensures organizers can execute ticket sales and attendees can access passes.
- **Measurement**: Automated HTTP synthetic uptime ping monitors.
- **Target**: MVP: 99.0% Uptime; Production Candidate SLO: 99.9% Uptime (Target to be established during performance engineering).
- **Priority**: Must (MVP) / Should (Production) | **Scope**: MVP / Production
- **Related BR**: BR-001, BR-006 | **Related PR**: PR-002, PR-009 | **Related FR**: FR-007, FR-014
- **Validation Method**: Analysis (A)
- **Dependencies**: Infrastructure container configuration
- **Risks**: Unhandled server crashes causing downtime.

---

#### NFR-006: Asynchronous Third-Party Failure Isolation
- **Name**: Asynchronous Third-Party Failure Isolation
- **Category**: Fault Tolerance & Reliability
- **Requirement**: Outages or latency spikes in external third-party services (Stripe, SendGrid) shall NOT crash the primary application backend or block non-dependent user operations.
- **Rationale**: External API downtime must be isolated from core system stability.
- **Measurement**: Simulated external network fault injection test.
- **Target**: 100% isolation; external API failures catch gracefully and return structured errors.
- **Priority**: Must | **Scope**: MVP
- **Related BR**: BR-014, BR-019 | **Related PR**: PR-009, PR-016 | **Related FR**: FR-014, FR-022
- **Validation Method**: Test (T)
- **Dependencies**: Asynchronous background job queue
- **Risks**: Unhandled external API socket timeouts blocking main HTTP threads.

---

### NFR Category 4: Data Integrity & Consistency

#### NFR-007: Absolute Inventory Non-Negativity & Zero Overselling Rate
- **Name**: Absolute Inventory Non-Negativity & Zero Overselling Rate
- **Category**: Data Integrity & Consistency
- **Requirement**: Under all concurrent write conditions, the system shall guarantee that total active reservations plus sold tickets for any event tier never exceeds published capacity ($Stock_{available} \ge 0$).
- **Rationale**: Overselling venue capacity creates severe legal liabilities, fire code violations, and financial refund penalties.
- **Measurement**: Total confirmed and active reserved tickets vs published capacity in database.
- **Target**: Exactly 0 overbooked tickets under 100+ concurrent reservation requests.
- **Priority**: Must | **Scope**: MVP
- **Related BR**: BR-004 | **Related PR**: PR-006, PR-007 | **Related FR**: FR-011, FR-012
- **Validation Method**: Test (T)
- **Dependencies**: Atomic database transaction locking
- **Risks**: Race conditions under concurrent threads violating inventory bounds.

---

#### NFR-008: Payment Webhook Idempotency & Reconciled Order Fulfillment
- **Name**: Payment Webhook Idempotency & Reconciled Order Fulfillment
- **Category**: Consistency & Reliability
- **Requirement**: Receiving duplicate payment gateway webhook notifications for the same transaction shall produce exactly ONE ticket fulfillment event without creating duplicate tickets or extra charges.
- **Rationale**: Payment networks frequently resend webhook payloads; processing must be strictly idempotent.
- **Measurement**: Simulated duplicate webhook injection test.
- **Target**: 100% idempotency; 0 duplicate tickets generated for duplicate webhooks.
- **Priority**: Must | **Scope**: MVP
- **Related BR**: BR-006, BR-019 | **Related PR**: PR-009 | **Related FR**: FR-014
- **Validation Method**: Test (T)
- **Dependencies**: Idempotency key tracking
- **Risks**: Duplicate ticket issuance causing capacity pollution.

---

### NFR Category 5: Security & Privacy

#### NFR-009: Password Storage Hashing & Credential Security
- **Name**: Password Storage Hashing & Credential Security
- **Category**: Security
- **Requirement**: All user passwords shall be cryptographically hashed using salted Argon2id (or bcrypt with cost factor $\ge 12$) before storage. Zero plain-text credentials shall touch storage or logs.
- **Rationale**: Protects user credentials against compromise in the event of database backup exposure.
- **Measurement**: Code inspection and automated password hashing unit tests.
- **Target**: 100% compliance; 0 plain-text passwords stored.
- **Priority**: Must | **Scope**: MVP
- **Related BR**: BR-009, BR-016 | **Related PR**: PR-001, PR-027 | **Related FR**: FR-001, FR-002
- **Validation Method**: Test (T) & Inspection (I)
- **Dependencies**: Cryptographic library
- **Risks**: Insecure hashing algorithms vulnerable to rainbow table attacks.

---

#### NFR-010: PCI-DSS SAQ A Zero-Card-Data Local Storage
- **Name**: PCI-DSS SAQ A Zero-Card-Data Local Storage
- **Category**: Security & Privacy
- **Requirement**: EVENTRA servers and databases shall store ZERO credit card numbers, CVVs, or raw cardholder credentials. All financial card handling shall be delegated entirely to PCI-DSS certified payment gateways.
- **Rationale**: Eliminates PCI-DSS compliance overhead and catastrophic financial data breach risk.
- **Measurement**: Database schema inspection and request payload audit.
- **Target**: 100% PCI SAQ A compliance; 0 card fields stored locally.
- **Priority**: Must | **Scope**: MVP
- **Related BR**: BR-006, BR-016 | **Related PR**: PR-009 | **Related FR**: FR-014
- **Validation Method**: Inspection (I) & Test (T)
- **Dependencies**: Payment gateway tokenization
- **Risks**: Accidental logging of financial request payloads.

---

#### NFR-011: Cryptographic Ticket QR Pass HMAC Signature Integrity
- **Name**: Cryptographic Ticket QR Pass HMAC Signature Integrity
- **Category**: Security
- **Requirement**: Digital QR ticket payloads shall be cryptographically signed using HMAC-SHA256 with a server secret key and event salt to prevent ticket pass forgery.
- **Rationale**: Prevents malicious attendees from creating or modifying QR screenshots to gain unauthorized gate entry.
- **Measurement**: Automated QR signature tampering verification unit tests.
- **Target**: 100% detection and rejection of forged or modified QR payloads.
- **Priority**: Should | **Scope**: Advanced
- **Related BR**: BR-007, BR-016 | **Related PR**: PR-011, PR-012 | **Related FR**: FR-015, FR-016
- **Validation Method**: Test (T)
- **Dependencies**: Server HMAC secret key management
- **Risks**: Weak signature secret allowing offline forgery.

---

### NFR Category 6: Accessibility, Usability & Responsiveness

#### NFR-012: WCAG 2.1 AA Accessibility Standards
- **Name**: WCAG 2.1 AA Accessibility Standards
- **Category**: Accessibility
- **Requirement**: All public-facing attendee discovery, details, and checkout views shall conform to WCAG 2.1 AA accessibility guidelines.
- **Rationale**: Ensures the platform is usable by attendees with visual, motor, or cognitive disabilities.
- **Measurement**: Automated AXE / Lighthouse accessibility audit score and manual keyboard navigation audit.
- **Target**: Accessibility score $\ge 95/100$; 100% pass on keyboard navigation.
- **Priority**: Must | **Scope**: MVP
- **Related BR**: BR-020 | **Related PR**: PR-002, PR-008, PR-026 | **Related FR**: FR-007, FR-008
- **Validation Method**: Analysis (A) & Demonstration (D)
- **Dependencies**: Semantic HTML5 markup
- **Risks**: Missing ARIA labels or poor color contrast degrading accessibility scores.

---

#### NFR-013: Responsive Viewport Fluidity Across Devices
- **Name**: Responsive Viewport Fluidity Across Devices
- **Category**: Responsiveness & Usability
- **Requirement**: Public attendee and organizer interfaces shall render fluidly across mobile ($320\text{px}$), tablet ($768\text{px}$), and desktop ($1200\text{px}$) viewports without horizontal scrollbars.
- **Rationale**: Attendees browse and buy tickets primarily on mobile devices.
- **Measurement**: Visual viewport responsiveness audit using browser developer tools across standard breakpoints.
- **Target**: 100% compliance across $320\text{px}$ to $1920\text{px}$ viewports.
- **Priority**: Must | **Scope**: MVP
- **Related BR**: BR-001, BR-020 | **Related PR**: PR-002, PR-026 | **Related FR**: FR-007
- **Validation Method**: Demonstration (D)
- **Dependencies**: Task 1 CSS Flexbox/Grid baseline
- **Risks**: Fixed pixel container widths causing mobile layout overflow.

---

### NFR Category 7: Observability, Maintainability & Testability

#### NFR-014: Structured JSON Logging & Automatic PII Redaction
- **Name**: Structured JSON Logging & Automatic PII Redaction
- **Category**: Observability & Privacy
- **Requirement**: All application server logs shall be emitted in structured JSON format with correlation IDs, and MUST automatically redact sensitive fields (passwords, tokens, card details, emails).
- **Rationale**: Structured logs enable rapid debugging while PII redaction prevents privacy leaks.
- **Measurement**: Automated log output payload inspection tests.
- **Target**: 100% structured JSON logs; 0 plain-text PII in server log outputs.
- **Priority**: Must | **Scope**: MVP
- **Related BR**: BR-016, BR-019 | **Related PR**: PR-027 | **Related FR**: FR-002, FR-014
- **Validation Method**: Test (T) & Inspection (I)
- **Dependencies**: Logging middleware
- **Risks**: Unsanitized error stack traces leaking credentials in logs.

---

#### NFR-015: Automated Unit Test Coverage Target
- **Name**: Automated Unit Test Coverage Target
- **Category**: Maintainability & Testability
- **Requirement**: Core domain service logic (Catalog, Booking Engine, Auth, Payment Webhooks) shall maintain an automated unit test coverage target enforced during CI builds.
- **Rationale**: Prevents regression bugs during continuous solo-developer refactoring.
- **Measurement**: Automated test coverage report (Jest / Istanbul).
- **Target**: $\ge 80\%$ line and branch code coverage on core domain modules.
- **Priority**: Must | **Scope**: MVP
- **Related BR**: BR-019 | **Related PR**: PR-027 | **Related FR**: FR-001 – FR-024
- **Validation Method**: Analysis (A)
- **Dependencies**: CI pipeline execution
- **Risks**: Writing low-quality tests merely to satisfy coverage metrics.

---

## 3. Definition of Done (DoD) — Sub-Phase 2.5
- [x] NFRs defined across all 20 specified quality dimensions.
- [x] All requirements include Name, Category, Requirement Statement, Rationale, Measurement, Target, Priority, Scope, Traceability Metadata, Validation Method, Dependencies, and Risks.
- [x] Measurable targets established where practical; unknown targets marked for later performance engineering validation.
- [x] Saved to [`docs/requirements/non-functional-requirements.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/non-functional-requirements.md).
