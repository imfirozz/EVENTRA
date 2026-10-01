# EVENTRA — Problem & Opportunity Definition

---

## 1. Documented Assumptions vs. Engineering Conclusions

> [!NOTE] Assumptions
> 1. **Traffic Spikes**: Flash sales generate $10\times - 50\times$ baseline write traffic within a 5-minute window.
> 2. **Attendee Behavior**: Users abandon checkout if reservation holds are not guaranteed for at least 5 minutes during payment input.
> 3. **Venue Conditions**: Event gate entry networks may experience poor or intermittent cellular connectivity.

> [!IMPORTANT] Engineering Conclusions
> 1. **Inventory Locking**: Naive SQL updates (`UPDATE inventory SET count = count - 1`) are unacceptable due to race conditions. Atomic reservation holds (pessimistic DB lock or Redis TTL locks) are mandatory.
> 2. **State Machine Integrity**: Ticket state must explicitly transition through `AVAILABLE -> RESERVED -> PAID -> ISSUED -> CANCELLED/EXPIRED`.
> 3. **QR Verification**: QR passes must embed cryptographically signed payloads (HMAC) to enable rapid offline/online validation at gate entry.

---

## 2. Stakeholder Problem Matrix

### A. Problems Faced by Customers (Attendees)
- **Phantom Inventory & Checkout Drops**: Ticking a ticket as available, only to have the payment fail because another user purchased it milliseconds earlier.
- **Opaque Checkout**: Unexpected hidden fees and confusing multi-step checkout flows.
- **Lost Pass Friction**: Unorganized ticket delivery (e.g., lost emails, unformatted PDFs) causing delays at entry.

### B. Problems Faced by Event Organizers
- **Overselling & Inventory Pollution**: oversold venue capacity leading to compliance fines, refund overhead, and lost brand trust.
- **Unreliable Flash Sale Infrastructure**: Site crashes or database lockups when announcement emails or push notifications fire.
- **Ticket Fraud**: Unregistered resale or duplicated ticket copies presented at the gate.

### C. Problems Faced by Event Staff (Gate Inspectors)
- **Bottlenecked Entry Lines**: Slow paper check-ins or lagging online lookups creating long venue queues ($>45\text{s}$ per attendee).
- **Scanner Failures**: Scanners dependent on continuous live internet connectivity failing when venue coverage drops.

### D. Platform-Level Engineering Problems
- **Concurrency Race Conditions**: Concurrent database threads attempting to claim the last remaining ticket simultaneously.
- **Webhook State Drift**: Payment gateway callbacks arriving out-of-order, late, or failing, leaving bookings in an ambiguous state (`RESERVED` vs `PAID`).
- **Cascading Failures**: Database connection pool exhaustion caused by unthrottled read traffic on event detail pages during high-demand sales.

---

## 3. Existing Workflow Limitations

| Legacy Approach | Technical Failure Mode | EVENTRA Solution Baseline |
| :--- | :--- | :--- |
| Unlocked Database Decrement | Negative inventory stock (Overselling) | Atomic DB transaction locks with TTL reservation holds |
| Plain Text QR / PDF Passes | Screenshots shared with multiple attendees | Signed HMAC-SHA256 encrypted QR tokens with entry state check |
| Synchronous Payment Processing | Database connection exhaustion during slow gateway responses | Asynchronous webhook-driven state machine updates |

---

## 4. Opportunity Statement

**EVENTRA addresses the gap between fragile tutorial projects and bloated enterprise platforms by providing a resilient, modular full-stack event ticketing platform.** It guarantees **strict inventory correctness** during booking spikes, delivers **cryptographically verified digital passes**, and provides **streamlined organizer analytics and fast gate validation**.

---

## 5. Core Engineering Challenge

> The primary engineering challenge of EVENTRA is **achieving strict transactional consistency and inventory atomicity under concurrent read/write surges while maintaining a clean, modular monolith architecture.**

---

## 6. Why Booking Correctness & Inventory Consistency Matter

1. **Legal & Physical Capacity Constraints**: Venues have strict fire-code limits. Overselling even 1 ticket can result in denied entry, regulatory fines, and legal liability.
2. **Financial Integrity**: Double bookings force costly payment refund processing fees and chargeback penalties.
3. **User Trust**: A single overbooking incident permanently damages the platform's credibility for both organizers and attendees.

---

## 7. Formal Problem & Opportunity Statements

### Problem Statement
> *Traditional event registration systems and lightweight web apps fail under concurrent booking traffic, causing inventory overselling, database connection lockups, payment state drift, and slow, fraud-prone entry check-ins at physical venues.*

### Opportunity Statement
> *EVENTRA provides a high-reliability event ticketing platform built on a modular monolith foundation. By employing atomic reservation locks, webhook-driven payment state transitions, and HMAC-signed QR passes, EVENTRA guarantees zero overselling, rapid checkout, and instant gate verification.*

---

## 8. What EVENTRA Is NOT Trying to Solve (Out of Scope Boundary)

To maintain focus and avoid scope creep, EVENTRA explicitly excludes:
1. **Secondary Ticket Resale Marketplace**: No peer-to-peer ticket reselling, auctioning, or scalp market features.
2. **Physical CAD 3D Seat Mapping**: No interactive 3D stadium seat positioning engines (inventory is managed by ticket tiers e.g. GA, VIP, Early Bird).
3. **Enterprise CRM / Marketing Automation**: No built-in email drip campaigns, social media ad managers, or external CRM suites.
4. **Physical Point-of-Sale (POS) Hardware**: No custom hardware manufacturing (uses standard mobile web app scanners).

---

## 9. Definition of Done (DoD) — Sub-Phase 1.2
- [x] All 12 problem definition elements articulated with concrete technical depth.
- [x] Documented assumptions cleanly separated from engineering conclusions.
- [x] Out-of-scope boundaries explicitly defined to prevent premature feature creep.
- [x] Saved to [`docs/02_PROBLEM_AND_OPPORTUNITY.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/02_PROBLEM_AND_OPPORTUNITY.md).
