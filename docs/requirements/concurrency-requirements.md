# EVENTRA — Concurrency & High-Demand Requirement Specifications

---

## 1. Central Concurrency Invariant

> [!IMPORTANT] Core Invariant Specification
> **A limited inventory item MUST NOT become confirmed for more than one customer under any concurrency condition.**
> 
> $$ConfirmedTickets \le PublishedCapacity$$

---

## 2. Concurrency Scenario Behavioral Expectations

### Scenario 1: Two Customers Attempting Final Seat Simultaneously
- **Condition**: Exactly 1 ticket remaining ($Stock_{available} = 1$). Customer A and Customer B submit reservation requests at the same millisecond.
- **Required Behavior**: System processes requests atomically. Exactly ONE customer obtains a 300-second `RESERVED` hold lock. The second customer receives HTTP 409 Conflict: "Tickets no longer available."

### Scenario 2: Payment Completion Overlaps with 300s Expiration
- **Condition**: Customer submits payment at second 299; gateway webhook arrives at second 301 (after 300s expiration worker marked booking `EXPIRED`).
- **Required Behavior**: System detects payment received for `EXPIRED` booking $\rightarrow$ marks order `PAYMENT_MISMATCH_REFUND_PENDING` and triggers automatic refund workflow. System does NOT issue invalid tickets.

### Scenario 3: Duplicate Reservation Request (Double Click)
- **Condition**: Customer clicks "Reserve Tickets" button twice in rapid succession (100ms apart).
- **Required Behavior**: System processes first request, locks stock, and returns active reservation token. System detects duplicate request for same session/cart and returns existing active reservation without double-locking stock.

### Scenario 4: Cancellation Overlaps with Payment Webhook
- **Condition**: Customer clicks "Cancel Checkout" at same time payment gateway dispatches success webhook.
- **Required Behavior**: State machine enforces atomic ordering. Whichever transaction executes first determines final state (`CANCELLED` with refund or `PAID`). System maintains state consistency.

---

## 3. Definition of Done (DoD) — Concurrency Requirements
- [x] Central concurrency invariant specified.
- [x] Behavioral expectations defined for 4 critical concurrency race condition scenarios.
- [x] Saved to [`docs/requirements/concurrency-requirements.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/concurrency-requirements.md).
