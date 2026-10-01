# EVENTRA — Complete 8-Tier Requirements Traceability Matrix

---

## 1. 8-Tier Requirements Traceability Matrix ($\text{BR} \rightarrow \text{PR} \rightarrow \text{FR} \rightarrow \text{US} \rightarrow \text{UC} \rightarrow \text{AC} \rightarrow \text{BRULE} \rightarrow \text{NFR}$)

| Business Req (BR) | Product Req (PR) | Functional Req (FR) | User Story (US) | Acceptance Criteria (AC) | Business Rule (BRULE) | Non-Functional Req (NFR) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **BR-001** (Catalog Showcase) | **PR-002** (Public Showcase) | **FR-007** (Catalog Rendering) | **US-003** (Browse Catalog) | **AC-004** (Public Catalog Display) | **BRULE-008** (Organizer Ownership) | **NFR-001** (Page Budget) |
| **BR-004** (Anti-Overselling) | **PR-006** (Availability Badges) | **FR-011** (Stock Verification)<br>**FR-012** (Atomic Hold Lock) | **US-006** (Quantity)<br>**US-007** (5-Min Hold Lock) | **AC-006** (Sold-Out Badge)<br>**AC-010** (2 Users 1 Seat) | **BRULE-001** (Inventory Invariant)<br>**BRULE-009** (Capacity Floor) | **NFR-004** (Concurrency)<br>**NFR-007** (0 Oversell) |
| **BR-005** (Hold Reservation) | **PR-007** (Hold Timer)<br>**PR-008** (Checkout Form) | **FR-012** (Atomic Hold Lock)<br>**FR-013** (Automatic Expiration) | **US-007** (5-Min Hold Lock)<br>**US-008** (Countdown Timer)<br>**US-009** (Checkout Form) | **AC-008** (5-Min Atomic Lock)<br>**AC-009** (Hold Expiration) | **BRULE-002** (300s Hold Duration)<br>**BRULE-006** (Max 6 Order Cap) | **NFR-002** (API SLA)<br>**NFR-007** (Data Integrity) |
| **BR-006** (Payment Reconciled) | **PR-009** (Payment Placement)<br>**PR-010** (Confirmation View) | **FR-014** (Payment Fulfillment) | **US-010** (Complete Payment) | **AC-011** (Payment Fulfillment)<br>**AC-012** (Declined Card)<br>**AC-013** (Duplicate Webhook) | **BRULE-004** (Zero Card Storage)<br>**BRULE-005** (Webhook Signature) | **NFR-008** (Idempotency)<br>**NFR-010** (PCI SAQ A) |
| **BR-007** (Digital QR Passes) | **PR-011** (Digital Pass)<br>**PR-014** (PDF Download Pass) | **FR-015** (HMAC QR Generation) | **US-011** (Digital Pass View) | **AC-014** (Digital Pass Display)<br>**AC-021** (HMAC QR Tampering) | **BRULE-007** (HMAC QR Signing) | **NFR-011** (HMAC Signature) |
| **BR-008** (Gate Verification) | **PR-012** (Gate Scanner App) | **FR-016** (Gate QR Verification) | **US-012** (Door Scanner Scan) | **AC-015** (Valid QR Scan)<br>**AC-016** (Rescanning Used Pass) | **BRULE-006** (Single Entry Scan) | **NFR-003** (Sub-Second Latency) |
| **BR-009** (Customer Account) | **PR-001** (Attendee Auth)<br>**PR-013** (Order Portal) | **FR-001** (Registration)<br>**FR-002** (Login)<br>**FR-017** (Order History) | **US-001** (Register)<br>**US-002** (Login)<br>**US-013** (Order History) | **AC-001** (Registration)<br>**AC-002** (Duplicate Email)<br>**AC-003** (Invalid Credentials) | **BRULE-004** (Zero Card Storage) | **NFR-009** (Argon2id Hashing) |
| **BR-010** (Event Management) | **PR-018** (Event Wizard)<br>**PR-020** (Organizer Dashboard) | **FR-018** (Event Wizard)<br>**FR-019** (Event Publication) | **US-015** (Create Event) | **AC-017** (Create & Publish Event) | **BRULE-008** (Organizer Ownership)<br>**BRULE-009** (Capacity Floor) | **NFR-002** (API SLA) |
| **BR-016** (Security Baseline) | **PR-027** (Role Guard Protection) | **FR-004** (Protected Role Guards) | **US-002** (Login) | **AC-019** (Unauthorized Role) | **BRULE-008** (Resource Isolation) | **NFR-009** (Argon2id Hashing) |
| **BR-017** (Refund Support) | **PR-025** (Support Order Lookup) | **FR-023** (Support Order Lookup) | Support Order Cancellation | Support Order Cancellation | **BRULE-010** (Cancellation Refund) | **NFR-006** (Async Failure) |

---

## 2. Integrity Audit & Traceability Sign-Off

- **Orphaned Rules**: **NONE**. All 10 Business Rules map to functional and product requirements.
- **Rules Contradicting Acceptance Criteria**: **NONE**.
- **Complete Traceability**: 8-tier chain ($\text{BR} \rightarrow \text{PR} \rightarrow \text{FR} \rightarrow \text{US} \rightarrow \text{UC} \rightarrow \text{AC} \rightarrow \text{BRULE} \rightarrow \text{NFR}$) verified 100%.

---

## 3. Definition of Done (DoD) — Requirements Traceability
- [x] Complete 8-tier traceability chain established.
- [x] Saved to [`docs/requirements/requirement-traceability.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/requirement-traceability.md).
