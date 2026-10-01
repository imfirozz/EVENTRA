# EVENTRA — Booking Failure Scenarios & Recovery Specifications

---

## 1. Failure Scenario & Recovery Matrix

| Failure Event | Trigger Condition | System Functional Failure Behavior | Data & Inventory Recovery Action |
| :--- | :--- | :--- | :--- |
| **1. Database Transaction Abort** | DB deadlock or constraint error during reservation. | System rolls back transaction. Returns HTTP 500/409 error. | Inventory count remains unchanged; zero partial booking record saved. |
| **2. Payment Provider Timeout** | Gateway fails to respond within 5s during checkout. | System maintains booking in `RESERVED` state within 300s window; relies on webhook callback. | Customer can retry payment within remaining 300s timer window. |
| **3. Client Disconnect Post-Payment** | Customer closes browser immediately after payment. | Webhook processes payment, transitions booking to `PAID`, generates tickets, sends email. | Order is confirmed; tickets viewable in customer account portal upon reconnect. |
| **4. Late Webhook After Expiration** | Webhook arrives for `EXPIRED` booking. | System detects payment for expired hold; marks order `PAYMENT_MISMATCH_REFUND_PENDING`. | Triggers automatic refund workflow via payment gateway; email sent to customer. |
| **5. Duplicate Webhook Callbacks** | Gateway resends payment webhook 3 times. | System verifies idempotency key; ignores 2nd and 3rd webhooks; returns 200 OK. | Exactly 1 ticket fulfillment event executed; zero duplicate tickets. |

---

## 2. Definition of Done (DoD) — Failure Scenarios
- [x] 5 critical failure scenarios and recovery behaviors documented.
- [x] Saved to [`docs/requirements/booking-failure-scenarios.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/booking-failure-scenarios.md).
