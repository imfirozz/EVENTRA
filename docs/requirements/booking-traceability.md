# EVENTRA — Booking & Inventory Traceability Matrix

---

## 1. Booking Domain Traceability Chain

| Business Req (BR) | Product Req (PR) | Functional Req (FR) | Business Rule (BRULE) | Booking Req (REQ-BOOK) | Invariant (INV) | Acceptance Criteria (AC) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **BR-004** (Anti-Oversell) | **PR-006** (Badges) | **FR-011** (Stock Verification) | **BRULE-001** (Inventory Invariant) | **REQ-BOOK-001** (GA Capacity) | **INV-001** (Capacity Limit) | **AC-006** (Sold Out Badge)<br>**AC-010** (2 Users 1 Seat) |
| **BR-005** (Hold Reservation) | **PR-007** (Hold Timer) | **FR-012** (Atomic Hold Lock)<br>**FR-013** (Auto Expiration) | **BRULE-002** (300s Hold Duration) | **REQ-BOOK-002** (300s Hold Lock) | **INV-003** (Expiration Recovery) | **AC-008** (5-Min Atomic Lock)<br>**AC-009** (Hold Expiration) |
| **BR-006** (Payment Reconciled) | **PR-009** (Payment Placement) | **FR-014** (Payment Fulfillment) | **BRULE-004** (Zero Card Storage)<br>**BRULE-005** (Webhook Idempotency) | Sandbox & Live Payment Conversion | **INV-005** (Payment Before Paid)<br>**INV-008** (Zero Card Storage) | **AC-011** (Fulfillment)<br>**AC-012** (Declined Card)<br>**AC-013** (Duplicate Webhook) |
| **BR-007** (Digital Passes) | **PR-011** (Digital Pass) | **FR-015** (HMAC QR Generation) | **BRULE-007** (HMAC QR Signing) | Digital QR Ticket Creation | **INV-007** (HMAC QR Signature) | **AC-014** (Pass Display) |
| **BR-008** (Gate Verification) | **PR-012** (Gate App) | **FR-016** (Gate QR Verification) | **BRULE-006** (Single Entry Scan) | Gate Verification Scan | **INV-006** (Single Entry Check-in) | **AC-015** (Valid Scan)<br>**AC-016** (Rescan Used) |

---

## 2. Definition of Done (DoD) — Booking Traceability
- [x] Complete Booking domain traceability matrix established.
- [x] Saved to [`docs/requirements/booking-traceability.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/booking-traceability.md).
