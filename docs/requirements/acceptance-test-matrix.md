# EVENTRA — Acceptance Test Validation Matrix

---

## 1. Master Acceptance Test Matrix

---

### A. MVP Acceptance Test Matrix (`v0.1` – `v0.5` Baseline)

| Criteria ID | Scenario Title | Scenario Type | Target Validation Type | Automated vs Manual |
| :--- | :--- | :--- | :--- | :--- |
| **AC-001** | Successful Customer Registration | Positive | API / Integration Test | Automated (Supertest) |
| **AC-002** | Duplicate Email Registration | Negative | Integration Test | Automated (Supertest) |
| **AC-003** | Invalid Credentials Login Rejection | Negative / Security | Security / API Test | Automated (Jest) |
| **AC-004** | Public Event Catalog Showcase Display | Positive | Visual / E2E Verification | Manual / Playwright |
| **AC-005** | Category Tag Catalog Filtering | Positive | End-to-End Test | Automated (Playwright) |
| **AC-006** | Sold-Out Stock Availability Indicator | Boundary / Negative | Visual / API Test | Automated / Manual |
| **AC-007** | Maximum Ticket Order Cap (6 Tickets) | Boundary | Integration Test | Automated (Jest) |
| **AC-008** | 5-Minute Atomic Ticket Hold Lock | Positive / Concurrency | Concurrency / Integration | Automated (Jest concurrent) |
| **AC-009** | 5-Minute Hold Timer Expiration | Negative / Boundary | Integration Test | Automated (Jest fake timers) |
| **AC-010** | Two Customers Competing for Final Seat | Concurrency / Boundary | Concurrency Test | Automated (`Promise.all`) |
| **AC-011** | Verified Payment Order Fulfillment | Positive | Integration / API Test | Automated (Supertest) |
| **AC-012** | Payment Gateway Declined Card | Negative | Integration Test | Automated (Jest) |
| **AC-013** | Duplicate Payment Webhook Idempotency | Reliability | Integration Test | Automated (Supertest) |
| **AC-014** | Digital QR Pass Display | Positive | Visual / E2E Verification | Manual / Playwright |
| **AC-017** | Create & Publish New Event Listing | Positive | Integration / E2E Test | Automated (Supertest) |
| **AC-018** | Full Keyboard Booking Navigation | Accessibility | Accessibility Audit | Automated (AXE) / Manual |
| **AC-019** | Unauthorized Role Endpoint Rejection | Security | Security / API Test | Automated (Supertest) |

---

### B. Advanced Acceptance Test Matrix (`v0.8` Tier)

| Criteria ID | Scenario Title | Scenario Type | Target Validation Type | Automated vs Manual |
| :--- | :--- | :--- | :--- | :--- |
| **AC-015** | Valid QR Pass Door Gate Scan | Positive | Integration / E2E Test | Automated / Mobile Camera Demo |
| **AC-016** | Rescanning Already-Used Ticket Pass | Negative / Security | Integration Test | Automated (Supertest) |
| **AC-020** | Live Stripe Gateway Webhook Verification | Positive / Security | Webhook Signature Test | Automated (Stripe SDK mock) |
| **AC-021** | HMAC QR Signature Tampering Rejection | Security / Negative | Security Test | Automated (Jest) |
| **AC-022** | Support Ticket Cancellation & Refund | Positive | Integration Test | Automated (Supertest) |

---

### C. Production / Extreme Acceptance Test Matrix (`v1.0` Tier)

| Criteria ID | Scenario Title | Scenario Type | Target Validation Type | Automated vs Manual |
| :--- | :--- | :--- | :--- | :--- |
| **AC-023** | 500 RPS Flash-Sale Concurrency Load | Performance | Concurrency / Load Test | Automated (k6 load script) |
| **AC-024** | Super-Admin Event Suspension Audit | Security / Governance | Audit Log Inspection | Automated / Manual |
| **AC-025** | Offline PWA Gate Scanner Sync | Reliability / PWA | Service Worker Test | Manual / PWA Audit |

---

## 2. Definition of Done (DoD) — Acceptance Test Matrix
- [x] All 25 Acceptance Criteria mapped across MVP, Advanced, and Production testing tiers.
- [x] Target validation type (Unit, Integration, Concurrency, Security, Accessibility, E2E) assigned.
- [x] Saved to [`docs/requirements/acceptance-test-matrix.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/acceptance-test-matrix.md).
