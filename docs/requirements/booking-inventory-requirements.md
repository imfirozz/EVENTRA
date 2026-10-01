# EVENTRA — Booking & Inventory Requirements Specification

---

## 1. Domain Conceptual Model

The Booking and Inventory domain coordinates the reservation, purchasing, and allocation of event tickets. The conceptual entities are defined as follows:

- **Event**: The published event listing hosted by an Organizer.
- **Venue & Venue Section**: The physical or virtual space where the event takes place, optionally divided into sections.
- **General Admission (GA) Inventory**: Quantity-based ticket pool where attendees hold unassigned entry rights up to the total capacity limit.
- **Reserved Seat**: A specific physical or numbered seat (e.g. Row A, Seat 12) assigned to a single attendee.
- **Ticket Category / Tier**: Pricing and access level (e.g. Early Bird, General Admission, VIP).
- **Inventory Item / Unit**: A single discrete unit of stock (either 1 count of GA pool or 1 specific Seat).
- **Reservation Hold**: A temporary 300-second lock placed on an inventory unit during checkout.
- **Booking**: The customer order record tracking reservation, payment status, and order metadata.
- **Payment**: Financial transaction record authorizing ticket purchase.
- **Ticket**: Digital entry pass issued upon confirmed payment containing HMAC QR verification data.

---

## 2. Requirements Catalogue by Inventory Type

---

### A. General Admission (GA) Inventory Requirements

#### REQ-BOOK-001: Quantity-Based Inventory Availability Calculation
- **Requirement Statement**: THE SYSTEM SHALL calculate remaining available General Admission stock as:
  $$Stock_{available} = Capacity_{total} - Tickets_{sold} - Holds_{active}$$
- **Scope**: MVP | **Priority**: Must
- **Traceability**: BR-004 $\rightarrow$ PR-006 $\rightarrow$ FR-011 $\rightarrow$ US-006 $\rightarrow$ UC-004 $\rightarrow$ AC-006 $\rightarrow$ BRULE-001

---

#### REQ-BOOK-002: Atomic GA Inventory Hold Lock
- **Requirement Statement**: WHEN a customer selects a valid quantity ($Quantity \le Stock_{available}$ and $Quantity \le 6$), THE SYSTEM SHALL atomically reserve $Quantity$ units for 300 seconds, decrement $Stock_{available}$, and create a booking in `RESERVED` state.
- **Scope**: MVP | **Priority**: Must
- **Traceability**: BR-005 $\rightarrow$ PR-007 $\rightarrow$ FR-012 $\rightarrow$ US-007 $\rightarrow$ UC-005 $\rightarrow$ AC-008 $\rightarrow$ BRULE-002

---

### B. Reserved Seating Requirements (Advanced Tier)

#### REQ-BOOK-003: Numbered Seat Selection & Lock
- **Requirement Statement**: WHERE reserved seating is enabled, WHEN a customer selects specific numbered seats (e.g. Section 101, Row C, Seats 4–5), THE SYSTEM SHALL atomically lock those specific seat IDs for 300 seconds.
- **Scope**: Advanced | **Priority**: Should
- **Traceability**: BR-003 $\rightarrow$ PR-005 $\rightarrow$ FR-011 $\rightarrow$ US-006 $\rightarrow$ UC-005

---

### C. Scalability & High-Demand Event Handling

#### REQ-BOOK-004: High-Demand Flash-Sale Throughput Property
- **Requirement Statement**: THE SYSTEM SHALL maintain 0% overselling rate and 0 DB deadlocks under peak flash-sale write surges of up to 500 Requests Per Second.
- **Scope**: Production Target | **Priority**: Should
- **Traceability**: BR-004 $\rightarrow$ NFR-004 $\rightarrow$ NFR-007

---

## 3. Scope Tier Breakdown

### A. MVP Booking Requirements (`v0.1` – `v0.5`)
- `REQ-BOOK-001` (GA Capacity Calculation), `REQ-BOOK-002` (Atomic 300s Hold Lock), 6-Ticket Order Cap enforcement, 300s Expiration Worker, Sandbox Payment Conversion.

### B. Advanced Booking Requirements (`v0.8`)
- `REQ-BOOK-003` (Numbered Reserved Seating), Multi-Tier Pricing Setup, Live Stripe Gateway Webhook Reconciliation, Support Ticket Cancellation & Stock Recovery.

### C. Production / Extreme Booking Requirements (`v1.0`)
- `REQ-BOOK-004` (500 RPS Flash-Sale Concurrency Engine), Virtual Waiting Room Queue, PWA Offline Scanner Key Caching.

---

## 4. Definition of Done (DoD) — Booking Requirements
- [x] Conceptual domain model and inventory types defined.
- [x] GA and Reserved Seating requirements specified.
- [x] MVP vs Advanced vs Production tiers separated.
- [x] Saved to [`docs/requirements/booking-inventory-requirements.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/booking-inventory-requirements.md).
