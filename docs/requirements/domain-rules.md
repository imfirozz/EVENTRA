# EVENTRA — Domain Specific Policy Rules

---

## 1. Domain Policy Specifications

---

### Domain 1: Time, Expiration & Window Rules
- **Reservation Expiration Window**: Fixed at 300 seconds (5 minutes) from reservation initiation (BRULE-002).
- **Password Reset Token TTL**: Fixed at 900 seconds (15 minutes) from request.
- **Refund Eligibility Window**: *OPEN BUSINESS DECISION* — Refunds allowed up to 24 hours prior to event start time.
- **Event Start Gate Check-in Window**: Gate scanning opens 2 hours prior to published event start time and closes 1 hour after event start.

---

### Domain 2: Money, Pricing & Currency Rules
- **Base Currency**: Supported default base currency is USD (`$`). Multi-currency handling is *DEFERRED TO PHASE 5*.
- **Rounding Rule**: Financial totals rounded to 2 decimal places using standard half-up rounding (e.g. `$49.99`).
- **Platform Service Fees**: *OPEN BUSINESS DECISION* — Service fee structures (flat fee vs percentage per ticket) requiring organizer commercial review in Phase 5.
- **Tax Policy**: *OPEN BUSINESS DECISION* — Local sales tax calculation requiring legal/tax review prior to production launch.

---

### Domain 3: Event Staff & Verification Access Rules
- **Staff Assignment**: Event staff accounts MUST be explicitly assigned to specific event IDs by the event organizer.
- **Verification Authority**: Staff can ONLY verify tickets belonging to their assigned event ID.
- **Post-Scan Action**: Successful check-in immediately locks the ticket state (`CHECKED_IN`); staff cannot manually revert a ticket back to `PAID`.

---

### Domain 4: Notification Trigger Rules
- **Booking Confirmation**: Triggered asynchronously immediately upon booking state transition to `PAID`.
- **Payment Failure Notification**: Triggered immediately upon payment authorization rejection.
- **Event Cancellation Alert**: Triggered immediately upon organizer event cancellation or super-admin suspension.

---

## 2. Open Business Decisions & Clarifications Directory

1. **Refund Cutoff Window**: Exact hours prior to event start after which ticket refunds are strictly prohibited. *(Current Working Rule: 24 hours prior)*.
2. **Platform Service Fee Structure**: Whether EVENTRA charges a fixed \$1.00 per ticket or a 3% platform fee to event organizers. *(Current Working Rule: Deferred to Phase 5 Payments)*.
3. **Multi-Currency Processing**: Supporting localized currency conversions at checkout. *(Current Working Rule: Single USD base currency for MVP)*.

---

## 3. Definition of Done (DoD) — Domain Rules
- [x] Domain policies for Time, Money, Staff, Notifications, and Open Business Decisions documented.
- [x] Saved to [`docs/requirements/domain-rules.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/domain-rules.md).
