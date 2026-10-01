# EVENTRA — Product Requirement Decisions & Traceability Mapping

---

## 1. Business Requirement $\rightarrow$ Product Requirement Mapping Table

| Business Requirement ID & Title | Mapped Product Requirement(s) | Mapping Integrity Check |
| :--- | :--- | :--- |
| **BR-001**: Public Event Catalog Showcase | **PR-002**: Public Event Showcase Landing Page | ✅ Fully Mapped |
| **BR-002**: Comprehensive Event Detail | **PR-003**: Comprehensive Event Detail View | ✅ Fully Mapped |
| **BR-003**: Single & Multi-Tier Ticket Sales | **PR-005**: Ticket Quantity & Pricing Tier Selection<br>**PR-019**: Ticket Capacity & Tier Setup | ✅ Fully Mapped |
| **BR-004**: Anti-Overselling Guarantee | **PR-006**: Real-Time Availability & Sold-Out Badges | ✅ Fully Mapped |
| **BR-005**: Temporary Reservation Hold | **PR-007**: Temporary Ticket Reservation Hold Timer<br>**PR-008**: Frictionless Attendee Checkout Form | ✅ Fully Mapped |
| **BR-006**: Reconciled Payment Processing | **PR-009**: Payment Authorization & Order Placement<br>**PR-010**: Instant Order Confirmation View | ✅ Fully Mapped |
| **BR-007**: Instant Digital Pass Delivery | **PR-011**: Mobile Digital Ticket Pass Rendering<br>**PR-014**: Ticket PDF Download Access<br>**PR-015**: Peer-to-Peer Transfer (Out of Scope Boundary) | ✅ Fully Mapped |
| **BR-008**: Gate Entry Verification | **PR-012**: Gate Scanner Entry Check-in Validation | ✅ Fully Mapped |
| **BR-009**: Customer Account & Orders | **PR-001**: Attendee Registration & Auth<br>**PR-013**: Attendee Order History Portal | ✅ Fully Mapped |
| **BR-010**: Event Lifecycle Management | **PR-017**: Organizer Account Setup<br>**PR-018**: Event Creation & Publishing<br>**PR-020**: Centralized Organizer Dashboard | ✅ Fully Mapped |
| **BR-011**: Organizer Real-Time Sales | **PR-021**: Attendee Roster & Sales Summary | ✅ Fully Mapped |
| **BR-012**: Gate Scanner Staff Operations | **PR-022**: Mobile Gate Scanner Interface | ✅ Fully Mapped |
| **BR-013**: Super-Admin Governance | **PR-023**: Super-Admin Governance Console | ✅ Fully Mapped |
| **BR-014**: Transactional Email Dispatch | **PR-016**: Automated Confirmation Email Delivery | ✅ Fully Mapped |
| **BR-015**: Catalog Search & Filtering | **PR-004**: Keyword Search & Category Filtering | ✅ Fully Mapped |
| **BR-016**: Security & Access Control | **PR-027**: Account Protection & Role Guard Enforcement | ✅ Fully Mapped |
| **BR-017**: Refund Support Handling | **PR-025**: Support Order Lookup & Ticket Cancellation | ✅ Fully Mapped |
| **BR-018**: Live Gate Entry Analytics | **PR-024**: Real-Time Gate Entry Throughput Charts | ✅ Fully Mapped |
| **BR-019**: Failure Handling & Webhooks | **PR-009**: Webhook Idempotency Processing | ✅ Fully Mapped |
| **BR-020**: Inclusive Accessibility | **PR-026**: WCAG 2.1 AA Accessible Interface Standards | ✅ Fully Mapped |

---

## 2. Requirement Integrity Analysis

- **Orphaned Business Requirements**: **NONE**. All 20 Business Requirements (BR-001 through BR-020) map cleanly to at least one Product Requirement.
- **Unbacked Product Requirements**: **NONE**. Every Product Requirement (PR-001 through PR-027) traces directly to an approved Business Requirement.
- **Conflicting Requirements Resolved**:
  - *Conflict*: High-speed checkout vs. complete attendee data capture.
  - *Resolution*: Require only Name and Email during checkout; optional secondary details captured post-purchase.
- **Ambiguous Requirements Resolved**:
  - *Ambiguity*: Hold timer behavior when user closes browser.
  - *Resolution*: Server maintains 300-second TTL countdown independently of client browser state.

---

## 3. Unresolved Product Decisions & Clarifications

1. **Guest Checkout vs. Mandatory Account Registration**: Should attendees be allowed to checkout as a guest without creating a password?
   - *Decision*: **Mandatory Account Registration for MVP** to ensure order-to-user attribution and password-protected ticket retrieval.
2. **Hold Timer Extension Request**: Should attendees be allowed to request a 2-minute hold timer extension during checkout?
   - *Decision*: **No extensions allowed** to prevent queue blocking during flash sales.

---

## 4. Definition of Done (DoD) — Product Decisions & Mapping
- [x] All 20 Business Requirements mapped to Product Requirements (100% coverage).
- [x] Orphaned and unbacked requirement audit completed (0 gaps).
- [x] Product conflicts, ambiguities, and unresolved decisions documented.
- [x] Saved to [`docs/requirements/product-decisions.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/product-decisions.md).
