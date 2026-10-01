# EVENTRA — Organizer Access Control Requirements

---

## 1. Overview & Purpose

This document defines the access control boundaries for the Organizer role within EVENTRA. It specifies what organizers can and cannot access, enforces resource ownership isolation, and defines the relationship between organizers, events, staff, inventory, bookings, and attendees. No technical authorization mechanism (middleware, policy engine, RBAC implementation) is prescribed.

---

## 2. Role Hierarchy Context

EVENTRA defines the following roles (consistent with FR-004 and security-requirements-reference.md):

| Role            | Scope                   | Description                                                                           |
| :-------------- | :---------------------- | :------------------------------------------------------------------------------------ |
| **ATTENDEE**    | Customer-facing         | Can browse events, book tickets, view own orders.                                     |
| **ORGANIZER**   | Event management        | Can create/manage own events, configure inventory, view own event bookings/attendees. |
| **GATE_STAFF**  | Event-scoped operations | Can verify tickets (QR scan) for assigned events only.                                |
| **SUPER_ADMIN** | Platform-wide           | Global platform oversight, user management, event moderation.                         |

**Key Principle**: Each role has strictly bounded permissions. Higher roles do not automatically inherit lower-role permissions unless explicitly defined.

---

## 3. Organizer Access Control Matrix

### 3.1 Organizer → Event Access

| Action                                          | Permitted | Conditions                                               | Requirement ID |
| :---------------------------------------------- | :-------- | :------------------------------------------------------- | :------------- |
| Create event                                    | ✅ Yes    | Organizer account is `ACTIVE`.                           | ACC-EVT-001    |
| View own events                                 | ✅ Yes    | Event owned by this organizer.                           | ACC-EVT-002    |
| Edit own event                                  | ✅ Yes    | Event owned by this organizer, subject to editing rules. | ACC-EVT-003    |
| Publish own event                               | ✅ Yes    | Event owned by this organizer, validation passes.        | ACC-EVT-004    |
| Cancel own event                                | ✅ Yes    | Event owned by this organizer, event is cancellable.     | ACC-EVT-005    |
| View other organizer's events (management view) | ❌ No     | Strictly forbidden. BRULE-008.                           | ACC-EVT-006    |
| Edit other organizer's events                   | ❌ No     | Strictly forbidden. BRULE-008.                           | ACC-EVT-007    |
| Cancel other organizer's events                 | ❌ No     | Strictly forbidden. BRULE-008.                           | ACC-EVT-008    |
| Delete other organizer's events                 | ❌ No     | Strictly forbidden.                                      | ACC-EVT-009    |

### 3.2 Organizer → Staff Access

| Action                                    | Permitted | Conditions                     | Requirement ID |
| :---------------------------------------- | :-------- | :----------------------------- | :------------- |
| Assign staff to own event                 | ✅ Yes    | Event owned by this organizer. | ACC-STF-001    |
| Remove staff from own event               | ✅ Yes    | Event owned by this organizer. | ACC-STF-002    |
| View staff list for own event             | ✅ Yes    | Event owned by this organizer. | ACC-STF-003    |
| Assign staff to other organizer's event   | ❌ No     | Strictly forbidden.            | ACC-STF-004    |
| Remove staff from other organizer's event | ❌ No     | Strictly forbidden.            | ACC-STF-005    |

### 3.3 Organizer → Inventory Access

| Action                                       | Permitted | Conditions                                         | Requirement ID |
| :------------------------------------------- | :-------- | :------------------------------------------------- | :------------- |
| Configure inventory for own event            | ✅ Yes    | Event owned by this organizer.                     | ACC-INV-001    |
| View inventory counts for own event          | ✅ Yes    | Event owned by this organizer.                     | ACC-INV-002    |
| Modify inventory for own event               | ✅ Yes    | Subject to inventory adjustment rules (BRULE-009). | ACC-INV-003    |
| View inventory for other organizer's event   | ❌ No     | Strictly forbidden.                                | ACC-INV-004    |
| Modify inventory for other organizer's event | ❌ No     | Strictly forbidden.                                | ACC-INV-005    |

### 3.4 Organizer → Booking Access

| Action                                    | Permitted | Conditions                                                                                                      | Requirement ID |
| :---------------------------------------- | :-------- | :-------------------------------------------------------------------------------------------------------------- | :------------- |
| View bookings for own event               | ✅ Yes    | Event owned by this organizer.                                                                                  | ACC-BKG-001    |
| View booking counts/metrics for own event | ✅ Yes    | Event owned by this organizer.                                                                                  | ACC-BKG-002    |
| View bookings for other organizer's event | ❌ No     | Strictly forbidden.                                                                                             | ACC-BKG-003    |
| Cancel a customer's booking               | ❌ No     | Organizer-initiated booking cancellation: _OPEN BUSINESS DECISION_. Support/admin action only in current model. | ACC-BKG-004    |
| Modify a customer's booking               | ❌ No     | Booking modification by organizer is not supported.                                                             | ACC-BKG-005    |

### 3.5 Organizer → Attendee Access

| Action                                     | Permitted                | Conditions                                                             | Requirement ID |
| :----------------------------------------- | :----------------------- | :--------------------------------------------------------------------- | :------------- |
| View attendee list for own event           | ✅ Yes                   | Event owned by this organizer. Attendee info limited per ORG-SEC-021.  | ACC-ATT-001    |
| Search/filter attendees for own event      | ✅ Yes (Advanced)        | Event owned by this organizer.                                         | ACC-ATT-002    |
| View attendee payment details              | ❌ No                    | Strictly forbidden. ORG-SEC-022.                                       | ACC-ATT-003    |
| View attendee account passwords            | ❌ No                    | Strictly forbidden. ORG-SEC-022.                                       | ACC-ATT-004    |
| View attendees for other organizer's event | ❌ No                    | Strictly forbidden.                                                    | ACC-ATT-005    |
| Access unrelated customer accounts         | ❌ No                    | Strictly forbidden. Organizer sees only attendees of their own events. | ACC-ATT-006    |
| Export attendee data                       | _OPEN BUSINESS DECISION_ | Requires privacy review. ORG-SEC-023.                                  | ACC-ATT-007    |

### 3.6 Organizer → Platform Admin Access

| Action                     | Permitted | Conditions                                        | Requirement ID |
| :------------------------- | :-------- | :------------------------------------------------ | :------------- |
| Access admin dashboard     | ❌ No     | Organizer role does not grant admin access.       | ACC-ADM-001    |
| Manage other users         | ❌ No     | Strictly forbidden.                               | ACC-ADM-002    |
| Suspend/ban users          | ❌ No     | Strictly forbidden.                               | ACC-ADM-003    |
| View platform-wide metrics | ❌ No     | Organizer sees only metrics for their own events. | ACC-ADM-004    |
| Moderate content           | ❌ No     | Strictly forbidden.                               | ACC-ADM-005    |

---

## 4. Organizer Isolation Rules

### 4.1 Core Isolation Principles

| Principle                     | Rule                                                                                                                                  | Traceability                        |
| :---------------------------- | :------------------------------------------------------------------------------------------------------------------------------------ | :---------------------------------- |
| **Resource Ownership**        | An organizer can only access resources (events, inventory, bookings, attendees, staff) belonging to events they own.                  | BRULE-008                           |
| **Cross-Organizer Isolation** | Organizer A must never access, view, or modify any resource belonging to Organizer B.                                                 | BRULE-008, ORG-SEC-011, ORG-SEC-012 |
| **Role Boundary**             | An organizer must not gain `SUPER_ADMIN` or `GATE_STAFF` capabilities unless explicitly assigned those roles separately.              | STAFF-008, ACC-ADM-001              |
| **Data Minimization**         | Organizer access to attendee data is limited to the minimum necessary for event operations (name, email, booking status, attendance). | ORG-SEC-021                         |
| **Financial Data Boundary**   | Organizers see aggregate sales data only, not individual payment instrument details.                                                  | ORG-SEC-030, ORG-SEC-031            |

### 4.2 Isolation Enforcement Requirements

| Requirement ID  | Requirement                                                                                                                      | Priority | Scope    |
| :-------------- | :------------------------------------------------------------------------------------------------------------------------------- | :------- | :------- |
| **ACC-ISO-001** | Every organizer API call or dashboard action must verify that the target resource belongs to the authenticated organizer.        | Must     | MVP      |
| **ACC-ISO-002** | Unauthorized access attempts must return an authorization error (consistent with FR-004: 403 Forbidden).                         | Must     | MVP      |
| **ACC-ISO-003** | Error responses for unauthorized access must not leak information about the existence or content of other organizers' resources. | Must     | MVP      |
| **ACC-ISO-004** | Super-administrators retain global platform oversight and can access any organizer's events for moderation purposes.             | Should   | Advanced |

---

## 5. Delegation & Shared Access (Future)

| Requirement ID  | Requirement                                                                                             | Priority                                | Scope      |
| :-------------- | :------------------------------------------------------------------------------------------------------ | :-------------------------------------- | :--------- |
| **ACC-DEL-001** | Multi-user organization support (multiple organizer users managing events under a shared organization). | _OPEN BUSINESS DECISION_ (OBD-ORG-001). | Production |
| **ACC-DEL-002** | Co-organizer delegation (granting another organizer limited access to manage a specific event).         | _OPEN BUSINESS DECISION_.               | Production |
| **ACC-DEL-003** | Organizer sub-roles (e.g., "Event Editor" vs "Event Viewer" within an organization).                    | _OPEN BUSINESS DECISION_.               | Production |

---

## 6. Access Control Test Scenarios

| Test Scenario                                               | Expected Result                     | Test Type               |
| :---------------------------------------------------------- | :---------------------------------- | :---------------------- |
| Organizer A requests Organizer B's event detail.            | 403 Forbidden.                      | Authorization, Security |
| Organizer A attempts to edit Organizer B's event.           | 403 Forbidden.                      | Authorization, Security |
| Organizer A requests attendee list for Organizer B's event. | 403 Forbidden.                      | Authorization, Security |
| Organizer A assigns staff to Organizer B's event.           | 403 Forbidden.                      | Authorization, Security |
| Organizer A views bookings for Organizer B's event.         | 403 Forbidden.                      | Authorization, Security |
| Organizer accesses admin dashboard.                         | 403 Forbidden.                      | Authorization           |
| Organizer views attendee payment card details.              | Data not present in response / 403. | Privacy, Security       |
| Suspended organizer attempts to publish event.              | Authorization error.                | Authorization           |
| Attendee attempts to access organizer dashboard.            | 403 Forbidden.                      | Authorization           |
| Gate staff attempts to access organizer dashboard.          | 403 Forbidden.                      | Authorization           |

---

## 7. Open Business Decisions

| Decision ID     | Decision Area                  | Description                                                                                                                   |
| :-------------- | :----------------------------- | :---------------------------------------------------------------------------------------------------------------------------- |
| **OBD-ACC-001** | Organizer Booking Cancellation | Whether organizers can directly cancel a customer's booking for their event (or if this requires support/admin intervention). |
| **OBD-ACC-002** | Multi-User Organizations       | Whether multiple organizer accounts can share management of events under an organization.                                     |
| **OBD-ACC-003** | Co-Organizer Delegation        | Whether an organizer can delegate limited access to another organizer for a specific event.                                   |
| **OBD-ACC-004** | Organizer Sub-Roles            | Whether fine-grained sub-roles within the organizer role are needed (viewer, editor, manager).                                |

---

## 8. Definition of Done — Organizer Access Control

- [x] Organizer access control matrix defined (Events, Staff, Inventory, Bookings, Attendees, Admin).
- [x] Cross-organizer isolation rules defined.
- [x] Isolation enforcement requirements defined.
- [x] Role boundary constraints defined.
- [x] Data minimization and financial data boundaries defined.
- [x] Delegation and shared access (future) identified.
- [x] Access control test scenarios specified.
- [x] Open business decisions catalogued.
- [x] No technical authorization mechanism prematurely selected.
- [x] Saved to [`docs/requirements/organizer-access-control.md`](docs/requirements/organizer-access-control.md).
