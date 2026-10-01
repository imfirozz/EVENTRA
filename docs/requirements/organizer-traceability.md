# EVENTRA — Organizer Requirements Traceability Matrix

---

## 1. Overview & Purpose

This document provides the complete traceability chain for organizer requirements, mapping from Business Requirements through Product Requirements, Functional Requirements, Business Rules, Organizer Requirements, Acceptance Criteria, and Future Tests. It also identifies missing requirements, conflicting requirements, ambiguous policies, and decisions requiring resolution.

---

## 2. Organizer Requirements Traceability Matrix

### 2.1 Full Traceability Chain: BR → PR → FR → BRULE → ORG-REQ → Acceptance Criteria → Future Test

| Business Req                   | Product Req                      | Functional Req                                            | Business Rule                                                       | Organizer Requirement(s)                                                                          | Acceptance Criteria                                                                                      | Future Test Area                        |
| :----------------------------- | :------------------------------- | :-------------------------------------------------------- | :------------------------------------------------------------------ | :------------------------------------------------------------------------------------------------ | :------------------------------------------------------------------------------------------------------- | :-------------------------------------- |
| **BR-010** (Event Management)  | **PR-018** (Event Wizard)        | **FR-018** (Event Wizard)                                 | **BRULE-008** (Ownership Isolation), **BRULE-009** (Capacity Floor) | EVT-CRE-001 through EVT-CRE-007, EVT-EDT-001 through EVT-EDT-005                                  | Organizer creates event in DRAFT; publishes after validation; edits permitted fields per phase.          | Event Creation, Event Editing           |
| **BR-010** (Event Management)  | **PR-019** (Event Publication)   | **FR-019** (Event Publication)                            | **BRULE-008**                                                       | EVT-PUB-001 through EVT-PUB-012                                                                   | Publication succeeds only when all required fields pass validation; rejected with error list otherwise.  | Event Publishing                        |
| **BR-010** (Event Management)  | **PR-020** (Organizer Dashboard) | **FR-018** (Event Wizard), **FR-019** (Event Publication) | **BRULE-008**                                                       | ORG-DASH-010 through ORG-DASH-019                                                                 | Dashboard displays events filtered by status; shows sales counts; shows draft events needing completion. | Dashboard                               |
| **BR-003** (Ticket Sales)      | **PR-005** (Ticket Selection)    | **FR-011** (Stock Verification)                           | **BRULE-001** (Inventory Invariant), **BRULE-003** (6-Ticket Cap)   | TKT-CFG-001 through TKT-CFG-009, PRC-001 through PRC-006                                          | Organizer configures ticket categories with name, price, quantity; system enforces capacity constraints. | Ticket Configuration, Pricing           |
| **BR-004** (Anti-Overselling)  | **PR-006** (Availability Badges) | **FR-011** (Stock Verification), **FR-012** (Hold Lock)   | **BRULE-001**, **BRULE-009**                                        | INV-001 through INV-006, INV-AVAIL-001 through INV-AVAIL-003, INV-ADJ-001 through INV-ADJ-006     | Inventory never goes negative; capacity decrease rejected below sold+held count.                         | Inventory Configuration, Concurrency    |
| **BR-004** (Anti-Overselling)  | **PR-006** (Availability)        | **FR-011**, **FR-012**                                    | **BRULE-009** (Capacity Floor)                                      | VEN-001 through VEN-005, VEN-GA-001 through VEN-GA-003, VEN-RS-001 through VEN-RS-007             | Venue capacity enforced; total tickets ≤ venue capacity; seats follow state machine.                     | Venue Configuration                     |
| **BR-016** (Security Baseline) | **PR-027** (Role Guards)         | **FR-004** (RBAC)                                         | **BRULE-008** (Ownership Isolation)                                 | ACC-EVT-001 through ACC-EVT-009, ACC-ISO-001 through ACC-ISO-004, ORG-SEC-010 through ORG-SEC-012 | Organizer A cannot access Organizer B's resources; 403 returned for unauthorized access.                 | Access Control, Authorization, Security |
| **BR-016** (Security Baseline) | **PR-027** (Role Guards)         | **FR-004** (RBAC)                                         | **BRULE-008**                                                       | ACC-STF-001 through ACC-STF-005, STAFF-001 through STAFF-009                                      | Staff assigned only to owned events; staff cannot gain organizer privileges.                             | Staff Management, Authorization         |
| **BR-009** (Customer Account)  | **PR-001** (Attendee Auth)       | **FR-001** (Registration)                                 | —                                                                   | ORG-REG-001 through ORG-REG-006, ORG-PROF-001 through ORG-PROF-004                                | Organizer registers with name/email/password; profile visible on event pages.                            | Organizer Onboarding                    |
| **BR-016** (Security Baseline) | **PR-027** (Role Guards)         | **FR-004** (RBAC)                                         | **BRULE-008**                                                       | ORG-SEC-020 through ORG-SEC-023, ACC-ATT-001 through ACC-ATT-007                                  | Organizer sees only attendee name/email/ticket/status for own events; no payment details.                | Attendee Access, Privacy                |
| **BR-017** (Refund Support)    | **PR-025** (Support Lookup)      | **FR-023** (Support Lookup), **FR-024**                   | **BRULE-010** (Cancellation Refund)                                 | EVT-CAN-001 through EVT-CAN-010                                                                   | Cancelled event flags all bookings for refund; inventory restored; attendees notified.                   | Event Cancellation                      |
| **BR-010** (Event Management)  | **PR-020** (Dashboard)           | —                                                         | —                                                                   | SALES-001 through SALES-009, ANLY-001 through ANLY-010                                            | Organizer views sold/reserved/available counts; breakdown by category; revenue summary.                  | Sales Management, Analytics             |
| **BR-016** (Security Baseline) | —                                | —                                                         | —                                                                   | ORG-SEC-001 through ORG-SEC-003, ORG-SEC-030, ORG-SEC-031, ORG-SEC-040, ORG-SEC-041               | Passwords hashed; sessions use secure cookies; audit trail for critical actions.                         | Security                                |
| —                              | —                                | —                                                         | —                                                                   | ORG-NOTIF-001 through ORG-NOTIF-012                                                               | Organizer receives notifications for publication, bookings, cancellations, suspensions.                  | Notifications                           |
| —                              | —                                | —                                                         | —                                                                   | ORG-FAIL-001 through ORG-FAIL-013                                                                 | System returns appropriate errors for all failure scenarios.                                             | Negative Testing                        |
| —                              | —                                | —                                                         | —                                                                   | LIFE-ACCT-001 through LIFE-ACCT-006                                                               | Account defaults to ACTIVE; suspension blocks publishing; deactivation is terminal.                      | Account Lifecycle                       |

---

## 3. Organizer Requirement Coverage Summary

### 3.1 Requirements by Document

| Document                           | Requirement ID Range                                                               | Count | Coverage Status |
| :--------------------------------- | :--------------------------------------------------------------------------------- | :---- | :-------------- |
| `organizer-requirements.md`        | ORG-REG, ORG-PROF, ORG-ACCT, ORG-DASH, ORG-NOTIF, ORG-FAIL, ORG-SEC, LIFE-ACCT     | ~65   | ✅ Complete     |
| `event-management-requirements.md` | EVT-CRE, EVT-EDT, EVT-PUB, EVT-CAN, TKT-CFG, TKT-POL, PRC, SALES, ATT, STAFF, ANLY | ~85   | ✅ Complete     |
| `venue-inventory-requirements.md`  | VEN, VEN-GA, VEN-RS, VEN-ACC, INV, INV-AVAIL, INV-SALE, INV-ADJ                    | ~35   | ✅ Complete     |
| `organizer-access-control.md`      | ACC-EVT, ACC-STF, ACC-INV, ACC-BKG, ACC-ATT, ACC-ADM, ACC-ISO, ACC-DEL             | ~40   | ✅ Complete     |
| `organizer-lifecycle.md`           | LIFE-ACCT, Workflow stages, Operational sequences                                  | ~10   | ✅ Complete     |

### 3.2 Business Rule Traceability

| Business Rule                                 | Organizer Requirements Mapped                                                              | Coverage  |
| :-------------------------------------------- | :----------------------------------------------------------------------------------------- | :-------- |
| **BRULE-001** (Inventory Non-Negativity)      | INV-AVAIL-001, INV-AVAIL-003, VEN-GA-003                                                   | ✅ Traced |
| **BRULE-003** (6-Ticket Order Cap)            | TKT-CFG-009 (referenced via BR-003)                                                        | ✅ Traced |
| **BRULE-004** (Zero Card Storage)             | ORG-SEC-031 (organizer cannot access payment details)                                      | ✅ Traced |
| **BRULE-008** (Organizer Ownership Isolation) | ACC-EVT-006 through ACC-EVT-009, ACC-ISO-001 through ACC-ISO-004, ORG-SEC-011, ORG-SEC-012 | ✅ Traced |
| **BRULE-009** (Capacity Immutability Floor)   | INV-ADJ-002, ORG-FAIL-004, Event Modification Rules                                        | ✅ Traced |
| **BRULE-010** (Cancellation Refund)           | EVT-CAN-005, EVT-CAN-006, EVT-CAN-007                                                      | ✅ Traced |

---

## 4. Gap Analysis

### 4.1 Missing Organizer Requirements (Identified)

| Gap ID          | Area                                             | Description                                                       | Resolution                                        |
| :-------------- | :----------------------------------------------- | :---------------------------------------------------------------- | :------------------------------------------------ |
| **GAP-ORG-001** | Organization Entity                              | Multi-user organization support not yet defined.                  | Deferred: _OPEN BUSINESS DECISION_ (OBD-ORG-001). |
| **GAP-ORG-002** | Organizer Verification                           | Identity/business verification policy not established.            | Deferred: _OPEN BUSINESS DECISION_ (OBD-ORG-002). |
| **GAP-ORG-003** | Refund Policy (Organizer-Initiated Cancellation) | Automatic vs policy-based refund not determined.                  | Deferred: _OPEN BUSINESS DECISION_ (EVT-CAN-010). |
| **GAP-ORG-004** | Date/Venue Change After Sales                    | Notification and refund policy for event changes not established. | Deferred: _OPEN BUSINESS DECISION_.               |
| **GAP-ORG-005** | Price Change After Bookings                      | Policy for price reduction/increase after confirmed bookings.     | Deferred: _OPEN BUSINESS DECISION_ (PRC-007).     |

### 4.2 Conflicting Requirements (None Identified)

All organizer requirements have been cross-referenced against:

- Booking state machine (`booking-state-machine.md`)
- Inventory state machine (`inventory-state-machine.md`)
- Payment state machine (`payment-state-machine.md`)
- Entity lifecycle rules (`lifecycle-rules.md`)
- Business rules (`business-rules.md`)
- Domain rules (`domain-rules.md`)
- Security requirements (`security-requirements-reference.md`)

**Result**: No conflicts detected. All organizer requirements are consistent with existing specifications.

### 4.3 Ambiguous Organizer Policies

| Ambiguity ID    | Area                           | Ambiguity                                                                                            | Required Resolution         |
| :-------------- | :----------------------------- | :--------------------------------------------------------------------------------------------------- | :-------------------------- |
| **AMB-ORG-001** | Suspended Account Events       | Unclear whether published events are hidden, flagged, or remain visible when organizer is suspended. | Business decision required. |
| **AMB-ORG-002** | Deactivated Account Events     | Unclear what happens to events when organizer deactivates account.                                   | Business decision required. |
| **AMB-ORG-003** | Near-Event Cutoff Window       | "Near event start" editing restrictions — exact cutoff (24 hours?) not confirmed.                    | Business decision required. |
| **AMB-ORG-004** | Sales Reopen                   | Whether organizer can reopen sales after manually closing them.                                      | Business decision required. |
| **AMB-ORG-005** | Draft Deletion vs Cancellation | Whether unused draft events are deleted or cancelled.                                                | Business decision required. |
| **AMB-ORG-006** | Low-Inventory Threshold        | Threshold value and opt-in policy for low inventory alerts.                                          | Business decision required. |

### 4.4 Requirements Requiring Business Decisions

| Decision Required                          | Affected Requirements             | Impact                                               |
| :----------------------------------------- | :-------------------------------- | :--------------------------------------------------- |
| Organizer identity verification policy     | ORG-REG-006                       | Affects onboarding flow, publication permissions.    |
| Multi-user organization support            | OBD-ORG-001, ACC-DEL-001          | Affects access control, delegation model.            |
| Account deactivation event policy          | ORG-ACCT-004, OBD-LIFE-002        | Affects attendee experience, refund volume.          |
| Event date/venue change policy after sales | Event Modification Rules          | Affects attendee trust, refund obligations.          |
| Price change policy after bookings         | PRC-007                           | Affects organizer flexibility, buyer fairness.       |
| Refund policy for organizer cancellation   | EVT-CAN-010                       | Affects financial operations, customer satisfaction. |
| Platform service fee structure             | PRC-009                           | Affects organizer revenue, platform monetization.    |
| Tax calculation                            | PRC-010                           | Affects compliance, pricing display.                 |
| Attendee data export                       | ORG-SEC-023, ACC-ATT-007, ATT-006 | Affects privacy compliance.                          |
| Low-inventory notification threshold       | ORG-NOTIF-005                     | Affects notification volume.                         |

### 4.5 Requirements Requiring Technical Design (Deferred)

| Technical Decision                                     | Affected Requirements          | Phase    |
| :----------------------------------------------------- | :----------------------------- | :------- |
| Database schema for organizer, event, venue, inventory | All organizer domain entities  | Phase 3+ |
| API endpoint design (REST/GraphQL)                     | All organizer CRUD operations  | Phase 3+ |
| UI/UX component design                                 | Dashboard, event wizard, forms | Phase 3+ |
| Authorization middleware implementation                | All ACC-\* requirements        | Phase 3+ |
| Notification delivery infrastructure                   | All ORG-NOTIF-\* requirements  | Phase 3+ |
| Analytics pipeline and technology                      | All ANLY-\* requirements       | Phase 3+ |
| Audit logging infrastructure                           | ORG-SEC-040, ORG-SEC-041       | Phase 3+ |
| Real-time availability calculations                    | INV-AVAIL-\*, BRULE-001        | Phase 3+ |
| Concurrency control mechanisms                         | INV-ADJ-\*, BRULE-009          | Phase 3+ |
| Session management implementation                      | ORG-SEC-002, ORG-SEC-003       | Phase 3+ |

---

## 5. Complete Organizer Requirement Catalogue (Summary Index)

### 5.1 By Category

| Category                    | Requirement ID Prefix                     | Document                         |
| :-------------------------- | :---------------------------------------- | :------------------------------- |
| Organizer Registration      | ORG-REG-\*                                | organizer-requirements.md        |
| Organizer Profile           | ORG-PROF-\*                               | organizer-requirements.md        |
| Organizer Account Status    | ORG-ACCT-\*                               | organizer-requirements.md        |
| Organizer Dashboard         | ORG-DASH-\*                               | organizer-requirements.md        |
| Organizer Notifications     | ORG-NOTIF-\*                              | organizer-requirements.md        |
| Organizer Failure Scenarios | ORG-FAIL-\*                               | organizer-requirements.md        |
| Organizer Security          | ORG-SEC-\*                                | organizer-requirements.md        |
| Organizer Account Lifecycle | LIFE-ACCT-\*                              | organizer-lifecycle.md           |
| Event Creation              | EVT-CRE-\*                                | event-management-requirements.md |
| Event Editing               | EVT-EDT-\*                                | event-management-requirements.md |
| Event Publishing            | EVT-PUB-\*                                | event-management-requirements.md |
| Event Cancellation          | EVT-CAN-\*                                | event-management-requirements.md |
| Ticket Configuration        | TKT-CFG-_, TKT-POL-_                      | event-management-requirements.md |
| Pricing                     | PRC-\*                                    | event-management-requirements.md |
| Sales Management            | SALES-\*                                  | event-management-requirements.md |
| Attendee Management         | ATT-\*                                    | event-management-requirements.md |
| Staff Management            | STAFF-\*                                  | event-management-requirements.md |
| Analytics                   | ANLY-\*                                   | event-management-requirements.md |
| Venue Configuration         | VEN-_, VEN-GA-_, VEN-RS-_, VEN-ACC-_      | venue-inventory-requirements.md  |
| Inventory Configuration     | INV-_, INV-AVAIL-_, INV-SALE-_, INV-ADJ-_ | venue-inventory-requirements.md  |
| Access Control — Events     | ACC-EVT-\*                                | organizer-access-control.md      |
| Access Control — Staff      | ACC-STF-\*                                | organizer-access-control.md      |
| Access Control — Inventory  | ACC-INV-\*                                | organizer-access-control.md      |
| Access Control — Bookings   | ACC-BKG-\*                                | organizer-access-control.md      |
| Access Control — Attendees  | ACC-ATT-\*                                | organizer-access-control.md      |
| Access Control — Admin      | ACC-ADM-\*                                | organizer-access-control.md      |
| Access Control — Isolation  | ACC-ISO-\*                                | organizer-access-control.md      |
| Access Control — Delegation | ACC-DEL-\*                                | organizer-access-control.md      |

### 5.2 Total Requirement Count by Scope

| Scope                       | Approximate Count | Status                     |
| :-------------------------- | :---------------- | :------------------------- |
| **MVP**                     | ~105              | ✅ Defined                 |
| **Advanced**                | ~65               | ✅ Defined                 |
| **Production**              | ~30               | ✅ Defined                 |
| **Open Business Decisions** | ~15               | ⏳ Awaiting Resolution     |
| **Total**                   | ~215              | ✅ Sub-Phase 2.13 Complete |

---

## 6. Open Business / Product Decisions — Consolidated

| ID           | Area                     | Description                                                 | Source                           |
| :----------- | :----------------------- | :---------------------------------------------------------- | :------------------------------- |
| OBD-ORG-001  | Organization Entities    | Multi-user organization support.                            | organizer-requirements.md        |
| OBD-ORG-002  | Identity Verification    | Organizer verification before publishing.                   | organizer-requirements.md        |
| OBD-ORG-003  | Account Deactivation     | Event fate on account deactivation.                         | organizer-requirements.md        |
| OBD-ORG-004  | Low-Inventory Threshold  | Notification threshold and opt-in policy.                   | organizer-requirements.md        |
| OBD-ORG-005  | Session Timeout          | Exact inactivity timeout duration.                          | organizer-requirements.md        |
| OBD-ORG-006  | Attendee Data Export     | Export permissions and privacy constraints.                 | organizer-requirements.md        |
| OBD-ACC-001  | Booking Cancellation     | Whether organizer can cancel customer bookings.             | organizer-access-control.md      |
| OBD-ACC-002  | Multi-User Orgs          | Multiple users under shared organization.                   | organizer-access-control.md      |
| OBD-ACC-003  | Co-Organizer Delegation  | Delegating event access to another organizer.               | organizer-access-control.md      |
| OBD-ACC-004  | Organizer Sub-Roles      | Fine-grained roles within organizer role.                   | organizer-access-control.md      |
| OBD-VEN-001  | Venue Templates          | Reusable venue configurations.                              | venue-inventory-requirements.md  |
| OBD-VEN-002  | Venue Change After Sales | Policy for changing venue with existing bookings.           | venue-inventory-requirements.md  |
| OBD-INV-001  | Inventory Reallocation   | Moving quantity between categories after sales.             | venue-inventory-requirements.md  |
| OBD-INV-002  | Oversold Waitlist        | Waitlist support when inventory reaches zero.               | venue-inventory-requirements.md  |
| OBD-LIFE-001 | Suspended Events         | Event visibility when organizer is suspended.               | organizer-lifecycle.md           |
| OBD-LIFE-002 | Deactivated Events       | Event fate when organizer deactivates account.              | organizer-lifecycle.md           |
| OBD-LIFE-003 | Sales Reopen             | Whether sales can be reopened after manual close.           | organizer-lifecycle.md           |
| OBD-LIFE-004 | Draft Deletion           | Permanent vs soft deletion of draft events.                 | organizer-lifecycle.md           |
| EVT-CAN-010  | Refund Policy            | Automatic vs policy-based refund on organizer cancellation. | event-management-requirements.md |
| PRC-007      | Price Changes            | Price change policy after confirmed bookings.               | event-management-requirements.md |
| PRC-008      | Discounts                | Promotional pricing / coupon codes.                         | event-management-requirements.md |
| PRC-009      | Platform Fees            | Service fee structure (flat vs percentage).                 | event-management-requirements.md |
| PRC-010      | Tax                      | Tax calculation and display.                                | event-management-requirements.md |
| PRC-011      | Multi-Currency           | Multi-currency support.                                     | event-management-requirements.md |

---

## 7. Technical Decisions to Postpone — Consolidated

| #   | Technical Decision                              | Rationale                                   |
| :-- | :---------------------------------------------- | :------------------------------------------ |
| 1   | Database schema                                 | Belongs to Phase 3 (Architecture & Design). |
| 2   | API endpoints (REST/GraphQL)                    | Belongs to Phase 3.                         |
| 3   | React/frontend components                       | Belongs to Phase 3.                         |
| 4   | Backend architecture (monolith/microservices)   | Belongs to Phase 3.                         |
| 5   | Redis / caching layer                           | Belongs to Phase 3+.                        |
| 6   | Queue / message broker                          | Belongs to Phase 3+.                        |
| 7   | Cloud infrastructure (AWS/Azure/GCP)            | Belongs to Phase 4+.                        |
| 8   | Specific libraries (ORM, validator, logger)     | Belongs to Phase 3.                         |
| 9   | Notification delivery (email/SMS/push provider) | Belongs to Phase 3+.                        |
| 10  | Analytics engine / BI tooling                   | Belongs to Phase 3+.                        |
| 11  | Authorization middleware pattern                | Belongs to Phase 3.                         |
| 12  | Audit log storage and format                    | Belongs to Phase 3+.                        |

---

## 8. Definition of Done — Sub-Phase 2.13

| #   | Criterion                                                  | Status       |
| :-- | :--------------------------------------------------------- | :----------- |
| 1   | Organizer identity is defined.                             | ✅ Complete  |
| 2   | Organizer onboarding is defined.                           | ✅ Complete  |
| 3   | Organizer dashboard capabilities are defined.              | ✅ Complete  |
| 4   | Event creation is defined.                                 | ✅ Complete  |
| 5   | Event editing is defined.                                  | ✅ Complete  |
| 6   | Event lifecycle is defined.                                | ✅ Complete  |
| 7   | Venue configuration is defined.                            | ✅ Complete  |
| 8   | Inventory configuration is defined.                        | ✅ Complete  |
| 9   | Ticket configuration is defined.                           | ✅ Complete  |
| 10  | Pricing behaviour is defined.                              | ✅ Complete  |
| 11  | Publishing is defined.                                     | ✅ Complete  |
| 12  | Cancellation behaviour is defined.                         | ✅ Complete  |
| 13  | Sales management is defined.                               | ✅ Complete  |
| 14  | Attendee management is defined.                            | ✅ Complete  |
| 15  | Staff management is defined.                               | ✅ Complete  |
| 16  | Organizer access-control boundaries are defined.           | ✅ Complete  |
| 17  | Event modification restrictions are documented.            | ✅ Complete  |
| 18  | Organizer analytics requirements are defined.              | ✅ Complete  |
| 19  | Organizer notifications are defined.                       | ✅ Complete  |
| 20  | Failure scenarios are documented.                          | ✅ Complete  |
| 21  | Security/privacy requirements are documented.              | ✅ Complete  |
| 22  | Future testing requirements are identified.                | ✅ Complete  |
| 23  | Traceability is complete.                                  | ✅ Complete  |
| 24  | No technical implementation has been prematurely selected. | ✅ Confirmed |

---

### Deliverables Produced

| #   | Deliverable                              | Location                                             |
| :-- | :--------------------------------------- | :--------------------------------------------------- |
| 1   | Complete Organizer Requirement Catalogue | `docs/requirements/organizer-requirements.md`        |
| 2   | Event Management Requirements            | `docs/requirements/event-management-requirements.md` |
| 3   | Venue & Inventory Requirements           | `docs/requirements/venue-inventory-requirements.md`  |
| 4   | Organizer Access-Control Matrix          | `docs/requirements/organizer-access-control.md`      |
| 5   | Organizer Lifecycle & Workflow           | `docs/requirements/organizer-lifecycle.md`           |
| 6   | Organizer Traceability Matrix            | `docs/requirements/organizer-traceability.md`        |

---

**Sub-Phase 2.13 — ORGANIZER REQUIREMENTS is COMPLETE.**

**Sub-Phase 2.14 has NOT been started.**
