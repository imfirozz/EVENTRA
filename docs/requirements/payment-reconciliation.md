# EVENTRA — Payment Reconciliation Specifications

---

## 1. Discrepancy Detection & Reconciliation Specifications

| Discrepancy Type | Internal State | Gateway State | Required Reconciliation Behavior | Resolution |
| :--- | :--- | :--- | :--- | :--- |
| **Type 1: Missed Webhook** | Booking = `RESERVED` | Payment = `SUCCEEDED` | System queries gateway transaction API; detects paid status; updates booking to `PAID` and fulfills tickets. | **Automated** |
| **Type 2: Late Payment** | Booking = `EXPIRED` | Payment = `SUCCEEDED` | System detects payment for expired hold; flags order `PAYMENT_MISMATCH_REFUND_PENDING`; triggers auto-refund. | **Automated** |
| **Type 3: Unknown Payment** | Booking = `PENDING` | Payment = `UNKNOWN` | System logs discrepancy; queries gateway after 15 mins; if unresolved, notifies support operator. | **Manual / Support** |
| **Type 4: External Refund** | Booking = `PAID` | Payment = `REFUNDED` | Webhook updates booking state to `CANCELLED`, invalidates tickets, and restores inventory stock. | **Automated** |

---

## 2. Definition of Done (DoD) — Payment Reconciliation
- [x] All 4 payment discrepancy types and reconciliation behaviors defined.
- [x] Saved to [`docs/requirements/payment-reconciliation.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/payment-reconciliation.md).
