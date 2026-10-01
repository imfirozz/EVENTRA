# EVENTRA — Entity Lifecycle & State Transition Specifications

---

## 1. Booking Entity Lifecycle State Table

| State Name | Business Meaning | Allowed Transitions | Forbidden Transitions | State Transition Trigger |
| :--- | :--- | :--- | :--- | :--- |
| **`PENDING`** | Customer is selecting ticket quantity on event page. | `RESERVED` | `PAID`, `CHECKED_IN` | Customer clicks "Reserve Tickets". |
| **`RESERVED`** | Inventory locked under 300s TTL hold timer. | `PAID`, `EXPIRED`, `CANCELLED` | `CHECKED_IN` | Atomic hold transaction succeeds. |
| **`PAID`** | Payment verified; tickets generated. | `CANCELLED` | `RESERVED`, `EXPIRED` | Payment confirmation webhook verified. |
| **`EXPIRED`** | 300s timer reached without payment. Stock restored. | None (Terminal) | `PAID`, `RESERVED` | Expiration worker runs at 300s TTL. |
| **`CANCELLED`** | Booking cancelled by user or support refund. | None (Terminal) | `PAID`, `RESERVED` | Customer or support operator cancels order. |

---

## 2. Payment Entity Lifecycle State Table

| State Name | Business Meaning | Allowed Transitions | Forbidden Transitions | State Transition Trigger |
| :--- | :--- | :--- | :--- | :--- |
| **`PENDING`** | Payment session created at checkout. | `SUCCESS`, `FAILED`, `CANCELLED` | `REFUNDED` | Customer clicks "Pay Now". |
| **`SUCCESS`** | Payment authorized by gateway. | `REFUNDED` | `FAILED`, `PENDING` | Payment gateway confirmation callback. |
| **`FAILED`** | Card declined or bank authorization error. | `PENDING` (Retry) | `SUCCESS` | Bank rejects payment authorization. |
| **`CANCELLED`** | Customer closed payment gateway modal. | `PENDING` | `SUCCESS` | Customer exits payment view. |
| **`REFUNDED`** | Authorized refund processed by support. | None (Terminal) | `SUCCESS`, `PENDING` | Support operator executes refund action. |

---

## 3. Ticket Entity Lifecycle State Table

| State Name | Business Meaning | Allowed Transitions | Forbidden Transitions | State Transition Trigger |
| :--- | :--- | :--- | :--- | :--- |
| **`ISSUED`** | Ticket generated following booking payment (`PAID`). | `CHECKED_IN`, `CANCELLED` | `ISSUED` | Booking state transitions to `PAID`. |
| **`CHECKED_IN`** | Validated at venue entrance by gate scanner. | None (Terminal) | `ISSUED`, `CANCELLED` | Staff camera scans valid HMAC QR pass. |
| **`CANCELLED`** | Ticket invalidated due to order refund or cancellation. | None (Terminal) | `CHECKED_IN`, `ISSUED` | Support processes ticket order cancellation. |

---

## 4. Event Entity Lifecycle State Table

| State Name | Business Meaning | Allowed Transitions | Forbidden Transitions | State Transition Trigger |
| :--- | :--- | :--- | :--- | :--- |
| **`DRAFT`** | Event listing created by organizer; hidden from public catalog. | `PUBLISHED` | `COMPLETED` | Organizer creates event. |
| **`PUBLISHED`** | Live event visible in public catalog; tickets available. | `CANCELLED`, `SUSPENDED`, `COMPLETED` | `DRAFT` | Organizer publishes event. |
| **`SUSPENDED`** | Event hidden from catalog due to admin policy moderation. | `PUBLISHED`, `CANCELLED` | `COMPLETED` | Super-admin executes suspension action. |
| **`CANCELLED`** | Event cancelled by host or admin; refunds triggered. | None (Terminal) | `PUBLISHED` | Organizer or admin cancels event. |
| **`COMPLETED`** | Event end date has passed. | None (Terminal) | `PUBLISHED` | System cron marks past events completed. |

---

## 5. Definition of Done (DoD) — Lifecycle Specifications
- [x] State transition tables for Booking, Payment, Ticket, and Event entities documented.
- [x] Meaning, allowed transitions, forbidden transitions, and triggers defined for every state.
- [x] Saved to [`docs/requirements/lifecycle-rules.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/lifecycle-rules.md).
