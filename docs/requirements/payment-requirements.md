# EVENTRA — Payment Requirements Specification

---

## 1. Conceptual Payment Domain Model

The Payment domain governs financial checkout authorization, webhook reconciliation, and refund workflows. The conceptual entities are defined as follows:

- **Booking**: The customer order tracking ticket reservation, customer contact, and fulfillment state.
- **Payment Attempt**: A single session or attempt to authorize payment for an active `RESERVED` booking.
- **Payment Transaction**: The verified record of a financial transaction authorized by the payment gateway.
- **Payment Status**: The current state of financial authorization (`INITIATED`, `PENDING`, `SUCCEEDED`, `FAILED`, `CANCELLED`, `REFUNDED`).
- **Refund**: A financial transaction returning funds from a paid booking to the original payment method.
- **Refund Status**: The state of refund execution (`REFUND_PENDING`, `REFUNDED`, `REFUND_FAILED`).

---

## 2. Requirements Catalogue by Payment Sub-Domain

---

### A. Payment Amount Integrity & Initiation Requirements

#### REQ-PAY-001: Strict Amount Integrity Calculation
- **Requirement Statement**: THE SYSTEM SHALL calculate payable checkout totals strictly as:
  $$PayableTotal = \sum (TicketQuantity \times TierPrice) + ApprovedServiceFees - ApprovedDiscounts$$
  THE SYSTEM SHALL reject payment authorization attempts where the payable amount submitted differs from the calculated booking total.
- **Scope**: MVP | **Priority**: Must
- **Traceability**: BR-006 $\rightarrow$ PR-009 $\rightarrow$ FR-014 $\rightarrow$ US-009, US-010 $\rightarrow$ AC-011

---

#### REQ-PAY-002: Zero Local Card Data Storage (PCI-DSS SAQ A)
- **Requirement Statement**: THE SYSTEM SHALL store ZERO raw credit card numbers, CVVs, or cardholder credentials on EVENTRA servers or database tables. All card handling MUST be delegated to PCI-DSS certified payment gateways.
- **Scope**: MVP | **Priority**: Must
- **Traceability**: BR-006, BR-016 $\rightarrow$ PR-009 $\rightarrow$ FR-014 $\rightarrow$ NFR-010 $\rightarrow$ AC-011 $\rightarrow$ BRULE-004

---

### B. Payment Confirmation & Webhook Reconciliation Requirements

#### REQ-PAY-003: Asynchronous Webhook Signature Verification
- **Requirement Statement**: THE SYSTEM SHALL verify the cryptographic signature of all incoming external payment gateway webhooks before mutating booking or ticket states. Unverified or forged webhook payloads MUST be rejected with HTTP 400/401 responses.
- **Scope**: MVP (Sandbox) / Advanced (Live Gateway) | **Priority**: Must
- **Traceability**: BR-006, BR-019 $\rightarrow$ PR-009 $\rightarrow$ FR-014 $\rightarrow$ NFR-008 $\rightarrow$ AC-013 $\rightarrow$ BRULE-005

---

#### REQ-PAY-004: Payment Webhook Idempotent Processing
- **Requirement Statement**: Receiving duplicate payment gateway webhooks for the same transaction ID SHALL produce exactly ONE ticket fulfillment event without issuing duplicate tickets or sending duplicate confirmation emails.
- **Scope**: MVP | **Priority**: Must
- **Traceability**: BR-006, BR-019 $\rightarrow$ PR-009 $\rightarrow$ FR-014 $\rightarrow$ NFR-008 $\rightarrow$ AC-013 $\rightarrow$ BRULE-005

---

### C. Delayed, Failed & Mismatched Payment Handling

#### REQ-PAY-005: Late Payment Webhook Mismatch Refund Handling
- **Requirement Statement**: WHEN a successful payment confirmation webhook arrives for a booking that has already reached `EXPIRED` status, THE SYSTEM SHALL mark the order `PAYMENT_MISMATCH_REFUND_PENDING` and trigger an automatic gateway refund workflow. THE SYSTEM SHALL NOT issue valid tickets for expired holds.
- **Scope**: MVP | **Priority**: Must
- **Traceability**: BR-006, BR-017 $\rightarrow$ PR-009, PR-025 $\rightarrow$ FR-014, FR-023 $\rightarrow$ AC-012

---

## 3. Scope Tier Breakdown

### A. MVP Payment Requirements (`v0.1` – `v0.5`)
- `REQ-PAY-001` (Amount Integrity), `REQ-PAY-002` (PCI SAQ A Zero-Card Storage), `REQ-PAY-003` (Sandbox Webhook Signature Validation), `REQ-PAY-004` (Idempotent Webhook Handler), `REQ-PAY-005` (Late Payment Refund Trigger).

### B. Advanced Payment Requirements (`v0.8`)
- Live Stripe Gateway Integration (`sk_live_...`), Support Order Refund Lookup API (`FR-023`), Event Cancellation Automated Refunds.

### C. Production / Extreme Payment Requirements (`v1.0`)
- Automated Payment Discrepancy Reconciliation Job, Multi-Currency Processing.

---

## 4. Definition of Done (DoD) — Payment Requirements
- [x] Conceptual payment domain model defined.
- [x] Amount integrity, PCI SAQ A, webhook signature, idempotency, and late payment requirements specified.
- [x] Saved to [`docs/requirements/payment-requirements.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/payment-requirements.md).
