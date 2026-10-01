# EVENTRA — Complete Business Rules Engine & Policy Specifications

---

## 1. Overview & Formatting Rules

This document specifies the authoritative **Business Rules (BRULE-XXX)** governing all operational policies, eligibility criteria, invariants, and constraints within EVENTRA. Business rules dictate system governance independently of software implementation mechanisms.

---

## 2. Master Business Rules Catalogue

---

### Category 1: Inventory & Concurrency Invariants

#### BRULE-001: Absolute Inventory Non-Negativity Invariant
- **Name**: Absolute Inventory Non-Negativity Invariant
- **Rule**: Total active reserved holds plus confirmed sold tickets for any event tier shall NEVER exceed published capacity limit ($Stock_{available} = Capacity_{total} - Tickets_{sold} - Holds_{active} \ge 0$).
- **Domain**: Inventory Engine
- **Reason**: Prevents overbooking venue capacity, fire code violations, and financial refund penalties.
- **Applies To**: All Customers, Event Organizers, System Booking Engine.
- **Related Requirements**: BR-004, PR-006, FR-011, FR-012, US-006, US-007, AC-010
- **Priority**: Must | **Scope**: MVP
- **Exceptions**: None. Invariant is absolute and non-negotiable.
- **Dependencies**: None
- **Validation Method**: Test (T)

---

#### BRULE-002: Temporary 300-Second Reservation Hold Duration
- **Name**: Temporary 300-Second Reservation Hold Duration
- **Rule**: Initiating a ticket reservation shall temporarily lock inventory for exactly 300 seconds (5 minutes). If payment checkout is not verified within 300 seconds, the hold reservation shall automatically expire and return inventory to public available stock.
- **Domain**: Booking & Inventory Engine
- **Reason**: Balances customer checkout input time with preventing stock hoarding during flash sales.
- **Applies To**: All Ticket Reservation Checkouts.
- **Related Requirements**: BR-005, PR-007, FR-012, FR-013, US-007, US-008, AC-008, AC-009
- **Priority**: Must | **Scope**: MVP
- **Exceptions**: Hold duration extensions are explicitly forbidden.
- **Dependencies**: BRULE-001
- **Validation Method**: Test (T)

---

#### BRULE-003: Maximum Ticket Order Cap (6 Tickets)
- **Name**: Maximum Ticket Order Cap
- **Rule**: A single customer transaction order shall be limited to reserving a maximum of 6 tickets.
- **Domain**: Booking & Checkout
- **Reason**: Mitigates automated scalping bots and ticket hoarding by individuals.
- **Applies To**: Customer Booking Transactions.
- **Related Requirements**: BR-003, PR-005, FR-011, US-006, AC-007
- **Priority**: Must | **Scope**: MVP
- **Exceptions**: Special group booking requests require manual organizer agreement.
- **Dependencies**: None
- **Validation Method**: Test (T)

---

### Category 2: Payments & Financial Reconciliation

#### BRULE-004: Zero Local Credit Card Storage (PCI SAQ A)
- **Name**: Zero Local Credit Card Storage
- **Rule**: EVENTRA servers and databases shall store ZERO credit card numbers, card CVVs, or raw cardholder details. Financial card handling MUST be delegated entirely to PCI-DSS compliant payment gateways.
- **Domain**: Security & Payments
- **Reason**: Eliminates PCI-DSS compliance overhead and catastrophic financial data breach exposure.
- **Applies To**: Payment Processing Pipeline.
- **Related Requirements**: BR-006, BR-016, PR-009, FR-014, NFR-010, AC-011
- **Priority**: Must | **Scope**: MVP
- **Exceptions**: None.
- **Dependencies**: External Payment Gateway
- **Validation Method**: Inspection (I) & Test (T)

---

#### BRULE-005: Payment Webhook Signature Validation & Idempotency
- **Name**: Payment Webhook Signature Validation & Idempotency
- **Rule**: The system MUST verify the cryptographic signature of incoming payment gateway webhooks before mutating booking states. Receiving duplicate payment webhooks for an already-paid transaction MUST produce exactly ONE ticket fulfillment event without creating extra tickets.
- **Domain**: Payments & System Reliability
- **Reason**: Prevents webhook spoofing and duplicate ticket generation caused by network retries.
- **Applies To**: Payment Gateway Callbacks.
- **Related Requirements**: BR-006, BR-019, PR-009, FR-014, NFR-008, AC-013
- **Priority**: Must | **Scope**: MVP
- **Exceptions**: None.
- **Dependencies**: Payment Gateway Integration
- **Validation Method**: Test (T)

---

### Category 3: Ticket Verification & Door Entry

#### BRULE-006: Single-Entry Door Gate Scan Enforcement
- **Name**: Single-Entry Door Gate Scan Enforcement
- **Rule**: A digital QR ticket pass holding status `PAID` can be scanned and updated to `CHECKED_IN` exactly ONCE. Any subsequent scan attempt for the same ticket MUST be rejected as `ALREADY USED`.
- **Domain**: Ticket Verification & Gate Operations
- **Reason**: Prevents duplicate venue entry using copied or shared QR screenshots.
- **Applies To**: Gate Staff Door Check-in.
- **Related Requirements**: BR-008, BR-012, PR-012, FR-016, US-012, AC-015, AC-016
- **Priority**: Should | **Scope**: Advanced
- **Exceptions**: Re-entry policy is governed by specific venue rules (default: single entry).
- **Dependencies**: BRULE-007
- **Validation Method**: Test (T)

---

#### BRULE-007: Cryptographic HMAC QR Pass Signing
- **Name**: Cryptographic HMAC QR Pass Signing
- **Rule**: Every issued digital QR ticket payload MUST contain an HMAC-SHA256 signature generated using a server secret key and event salt. Unsigned, tampered, or invalid payload signatures MUST be rejected at venue gate check-in.
- **Domain**: Security & Digital Tickets
- **Reason**: Prevents malicious attendees from forging or modifying QR pass payloads.
- **Applies To**: Digital QR Generation & Verification.
- **Related Requirements**: BR-007, BR-016, PR-011, FR-015, NFR-011, AC-014, AC-021
- **Priority**: Should | **Scope**: Advanced
- **Exceptions**: Basic unsigned QR used for MVP sandbox phase.
- **Dependencies**: None
- **Validation Method**: Test (T)

---

### Category 4: Organizer & Platform Administration

#### BRULE-008: Organizer Resource Ownership Isolation
- **Name**: Organizer Resource Ownership Isolation
- **Rule**: Event organizers shall ONLY edit, view sales metrics, or manage events explicitly created under their own organizer account ID. Cross-organizer data access is strictly forbidden.
- **Domain**: Security & Account Authorization
- **Reason**: Protects multi-tenant organizer data isolation and privacy.
- **Applies To**: Organizer Portal & API Routes.
- **Related Requirements**: BR-010, BR-016, PR-020, FR-004, FR-018, AC-019
- **Priority**: Must | **Scope**: MVP
- **Exceptions**: Super Administrators possess global platform oversight privileges.
- **Dependencies**: None
- **Validation Method**: Test (T)

---

#### BRULE-009: Published Capacity Immutability Floor
- **Name**: Published Capacity Immutability Floor
- **Rule**: Organizers shall NOT reduce total event tier capacity below the number of currently sold plus actively reserved tickets for a published event.
- **Domain**: Event & Inventory Management
- **Reason**: Prevents invalidating already confirmed ticket purchases.
- **Applies To**: Organizer Event Editing.
- **Related Requirements**: BR-010, PR-019, FR-018
- **Priority**: Must | **Scope**: MVP
- **Exceptions**: Event cancellation (BRULE-010).
- **Dependencies**: BRULE-001
- **Validation Method**: Test (T)

---

#### BRULE-010: Event Cancellation Ticket Invalidation & Refund Eligibility
- **Name**: Event Cancellation Ticket Invalidation & Refund Eligibility
- **Rule**: If an event is cancelled by the organizer or suspended by a super-admin, all active `PAID` tickets for that event MUST be marked `CANCELLED` and flagged for full refund reconciliation.
- **Domain**: Booking & Support Operations
- **Reason**: Protects consumer financial rights and handles event cancellations.
- **Applies To**: Event Cancellation Workflow.
- **Related Requirements**: BR-017, PR-025, FR-023, FR-024
- **Priority**: Should | **Scope**: Advanced
- **Exceptions**: None.
- **Dependencies**: None
- **Validation Method**: Test (T)

---

## 3. Definition of Done (DoD) — Business Rules
- [x] 10 core business rules formally specified with domain, rationale, applications, and exceptions.
- [x] Saved to [`docs/requirements/business-rules.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/business-rules.md).
