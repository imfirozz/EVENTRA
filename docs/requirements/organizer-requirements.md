# EVENTRA — Organizer Requirements Specification

---

## 1. Overview & Purpose

This document defines the complete **Organizer-Side Requirements** for EVENTRA. The organizer is the user responsible for creating and managing events, configuring venues and ticket inventory, publishing events, monitoring sales, managing attendees, and assigning event staff. These requirements define what organizers must be able to do and the business rules governing those actions. No technical implementation decisions (database schemas, API endpoints, backend architecture, infrastructure) are prescribed.

---

## 2. Organizer Domain Model

The following conceptual entities define the organizer domain:

### 2.1 Entity Definitions

| Entity                  | Description                                                                                                    | Key Relationships                                                                    |
| :---------------------- | :------------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------- |
| **Organizer**           | An authenticated user with the `ORGANIZER` role who creates and manages events.                                | Owns Events, manages Staff, views Attendees.                                         |
| **Organizer Account**   | The identity and profile record of a registered organizer, including credentials, profile, and account status. | Belongs to one Organizer.                                                            |
| **Organization**        | An optional business entity grouping that may be associated with an organizer (e.g., company name, logo).      | _OPEN BUSINESS DECISION_ — whether multi-user organizations are required beyond MVP. |
| **Event**               | A published or draft event listing managed by the organizer.                                                   | Owned by Organizer, has Venue, has Ticket Categories, has Inventory, has Bookings.   |
| **Venue**               | The physical or virtual location where the event takes place.                                                  | Belongs to Event, has Venue Configuration.                                           |
| **Venue Configuration** | The structural layout of a venue including sections, rows, seats, and general admission areas.                 | Belongs to Venue.                                                                    |
| **Seat**                | A specific numbered or named position within a venue section (reserved seating).                               | Belongs to Venue Configuration.                                                      |
| **Ticket Category**     | A named pricing tier (e.g., General Admission, VIP, Early Bird) with price, quantity, and availability.        | Belongs to Event.                                                                    |
| **Inventory**           | The total pool of tickets available for sale across all categories for an event.                               | Belongs to Event, constrained by Venue capacity.                                     |
| **Pricing**             | The monetary value assigned to a ticket category, including base price and any approved adjustments.           | Belongs to Ticket Category.                                                          |
| **Event Staff**         | Users assigned by the organizer to perform event-day operations (e.g., gate check-in).                         | Assigned to Event by Organizer, holds `GATE_STAFF` role.                             |
| **Attendee**            | A customer who has completed a booking for an event.                                                           | Has Booking, has Ticket.                                                             |
| **Booking**             | The order record connecting an attendee to an event, tracking reservation and payment state.                   | Belongs to Event, belongs to Attendee.                                               |
| **Sales**               | Aggregate metrics on ticket sales, revenue, and booking activity for an event.                                 | Derived from Bookings for an Event.                                                  |
| **Event Analytics**     | Summarized operational and performance data for an organizer's events.                                         | Derived from Sales, Bookings, Attendance for Events owned by Organizer.              |

### 2.2 Domain Relationship Summary

```
Organizer
├── Organizer Account
├── Organization (optional)
├── Event (1..*)
│   ├── Venue
│   │   └── Venue Configuration
│   │       ├── Sections / Rows / Seats (Reserved Seating)
│   │       └── General Admission Areas
│   ├── Ticket Category (1..*)
│   │   └── Pricing
│   ├── Inventory
│   ├── Booking (0..*)
│   │   └── Attendee
│   ├── Event Staff (0..*)
│   └── Event Analytics / Sales
```

---

## 3. Organizer Onboarding

### 3.1 Organizer Registration

| Requirement ID  | Requirement                                                                                                       | Priority                                                                                                                        | Scope      |
| :-------------- | :---------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------ | :--------- |
| **ORG-REG-001** | The platform must allow a user to register as an organizer using name, email, and password.                       | Must                                                                                                                            | MVP        |
| **ORG-REG-002** | Organizer registration must validate email format and enforce minimum password strength (8+ characters).          | Must                                                                                                                            | MVP        |
| **ORG-REG-003** | Duplicate email addresses must be rejected during organizer registration.                                         | Must                                                                                                                            | MVP        |
| **ORG-REG-004** | Upon successful registration, the system must assign the `ORGANIZER` role and create an active organizer account. | Must                                                                                                                            | MVP        |
| **ORG-REG-005** | Organizers must accept platform Terms of Service at registration.                                                 | Should                                                                                                                          | Advanced   |
| **ORG-REG-006** | Identity or business verification for organizers.                                                                 | _OPEN BUSINESS/COMPLIANCE DECISION_ — Verification requirements depend on legal jurisdiction and payment provider KYC policies. | Production |

### 3.2 Organizer Profile

| Requirement ID   | Requirement                                                                                                                    | Priority | Scope    |
| :--------------- | :----------------------------------------------------------------------------------------------------------------------------- | :------- | :------- |
| **ORG-PROF-001** | An organizer must be able to set and update their display name, contact email, and optional profile description.               | Must     | MVP      |
| **ORG-PROF-002** | An organizer must be able to upload an optional profile image/logo.                                                            | Should   | Advanced |
| **ORG-PROF-003** | An organizer may optionally provide organization name and description.                                                         | Should   | Advanced |
| **ORG-PROF-004** | Organizer profile information must be visible to attendees on event detail pages (display name and optional description only). | Must     | MVP      |

### 3.3 Account Status

| Status          | Meaning                                                                                                                         |
| :-------------- | :------------------------------------------------------------------------------------------------------------------------------ |
| **ACTIVE**      | Organizer account is operational; can create and manage events.                                                                 |
| **SUSPENDED**   | Account suspended by platform admin; cannot create or publish events; existing published events may be hidden per admin action. |
| **DEACTIVATED** | Account voluntarily deactivated by organizer; existing events follow deactivation policy.                                       |

| Requirement ID   | Requirement                                                                                                                                              | Priority | Scope    |
| :--------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------- | :------- | :------- |
| **ORG-ACCT-001** | New organizer accounts must default to `ACTIVE` status upon successful registration.                                                                     | Must     | MVP      |
| **ORG-ACCT-002** | A super-admin must be able to suspend an organizer account, triggering event visibility restrictions.                                                    | Should   | Advanced |
| **ORG-ACCT-003** | A suspended organizer must not be able to create new events or publish draft events.                                                                     | Should   | Advanced |
| **ORG-ACCT-004** | An organizer must be able to voluntarily deactivate their account. Behaviour regarding existing events upon deactivation is an _OPEN BUSINESS DECISION_. | Should   | Advanced |

### 3.4 Organizer Dashboard Access

| Requirement ID   | Requirement                                                                               | Priority | Scope |
| :--------------- | :---------------------------------------------------------------------------------------- | :------- | :---- |
| **ORG-DASH-001** | Upon login, an authenticated organizer must be directed to an organizer dashboard view.   | Must     | MVP   |
| **ORG-DASH-002** | The organizer dashboard must be accessible only to users with the `ORGANIZER` role.       | Must     | MVP   |
| **ORG-DASH-003** | Attendees, gate staff, and unauthenticated users must not access the organizer dashboard. | Must     | MVP   |

---

## 4. Organizer Dashboard Capabilities

The organizer dashboard provides a unified operational view of the organizer's events and activities.

### 4.1 Dashboard Information Requirements

| Requirement ID   | Dashboard Capability                                                                                   | Priority | Scope    |
| :--------------- | :----------------------------------------------------------------------------------------------------- | :------- | :------- |
| **ORG-DASH-010** | View a list of all events owned by the organizer.                                                      | Must     | MVP      |
| **ORG-DASH-011** | Filter events by status: `DRAFT`, `PUBLISHED`, `SALES_OPEN`, `SALES_CLOSED`, `CANCELLED`, `COMPLETED`. | Must     | MVP      |
| **ORG-DASH-012** | View total tickets sold, tickets reserved, and tickets available per event.                            | Must     | MVP      |
| **ORG-DASH-013** | View total confirmed bookings per event.                                                               | Must     | MVP      |
| **ORG-DASH-014** | View total revenue-related information per event (total sales value).                                  | Should   | Advanced |
| **ORG-DASH-015** | View recent booking activity (latest bookings).                                                        | Should   | Advanced |
| **ORG-DASH-016** | View event-related notifications (cancellations, warnings, low inventory).                             | Should   | Advanced |
| **ORG-DASH-017** | View operational issues or alerts (failed publications, inventory conflicts).                          | Should   | Advanced |
| **ORG-DASH-018** | View attendance counts for completed or in-progress events.                                            | Should   | Advanced |
| **ORG-DASH-019** | View draft events requiring completion before publishing.                                              | Must     | MVP      |

---

## 5. Organizer Notifications

### 5.1 Notification Requirements

| Requirement ID    | Notification Trigger                                                                                                   | Priority                                                          | Scope      |
| :---------------- | :--------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------- | :--------- |
| **ORG-NOTIF-001** | Organizer is notified when an event is successfully published.                                                         | Should                                                            | Advanced   |
| **ORG-NOTIF-002** | Organizer is notified when a new booking is confirmed for their event.                                                 | Should                                                            | Advanced   |
| **ORG-NOTIF-003** | Organizer is notified when a booking is cancelled or refunded for their event.                                         | Should                                                            | Advanced   |
| **ORG-NOTIF-004** | Organizer is notified when an event is suspended by a platform admin.                                                  | Must                                                              | MVP        |
| **ORG-NOTIF-005** | Organizer is notified when ticket inventory for a category drops below a configurable threshold (low-inventory alert). | _OPEN BUSINESS DECISION_ — Threshold value and opt-in policy TBD. | Production |
| **ORG-NOTIF-006** | Organizer is notified when a payment-related event occurs (refund processed, payment dispute).                         | Should                                                            | Advanced   |
| **ORG-NOTIF-007** | Organizer is notified when an event change fails validation or publication fails.                                      | Must                                                              | MVP        |

### 5.2 Notification Delivery

| Requirement ID    | Requirement                                                                 | Priority | Scope      |
| :---------------- | :-------------------------------------------------------------------------- | :------- | :--------- |
| **ORG-NOTIF-010** | Notifications must be viewable within the organizer dashboard.              | Must     | MVP        |
| **ORG-NOTIF-011** | Email notification delivery for critical events (suspension, cancellation). | Should   | Advanced   |
| **ORG-NOTIF-012** | Notification preferences (opt-in/opt-out per notification type).            | Should   | Production |

> **Note**: The specific notification delivery channel (email, SMS, push) is a technical implementation decision deferred to later phases.

---

## 6. Organizer Error & Failure Scenarios

### 6.1 Failure Scenario Matrix

| Scenario ID      | Failure Scenario                                                                      | Expected System Behaviour                                                                      | Priority | Scope    |
| :--------------- | :------------------------------------------------------------------------------------ | :--------------------------------------------------------------------------------------------- | :------- | :------- |
| **ORG-FAIL-001** | Organizer attempts to publish an event with incomplete required fields.               | System rejects publication and returns a list of missing/invalid fields.                       | Must     | MVP      |
| **ORG-FAIL-002** | Organizer attempts to publish an event with zero ticket categories configured.        | System rejects publication with error: "At least one ticket category is required."             | Must     | MVP      |
| **ORG-FAIL-003** | Organizer attempts to publish an event with zero total inventory.                     | System rejects publication with error: "Event must have available inventory to publish."       | Must     | MVP      |
| **ORG-FAIL-004** | Organizer attempts to reduce ticket capacity below sold + reserved count.             | System rejects the change and displays current sold/reserved count. Consistent with BRULE-009. | Must     | MVP      |
| **ORG-FAIL-005** | Organizer attempts to set a negative ticket price.                                    | System rejects with validation error: "Ticket price must be zero or greater."                  | Must     | MVP      |
| **ORG-FAIL-006** | Organizer attempts to modify an event they do not own.                                | System returns authorization error. Consistent with BRULE-008.                                 | Must     | MVP      |
| **ORG-FAIL-007** | Organizer attempts to publish an already-published event.                             | System ignores duplicate publication or returns informational message.                         | Must     | MVP      |
| **ORG-FAIL-008** | Organizer attempts to edit an event that has already started (past event start time). | System restricts modifications per event modification rules (Section 17 below).                | Should   | Advanced |
| **ORG-FAIL-009** | Organizer attempts to cancel an already-cancelled event.                              | System returns error: "Event is already cancelled."                                            | Must     | MVP      |
| **ORG-FAIL-010** | Organizer configures conflicting inventory (e.g., seat count exceeds venue capacity). | System rejects configuration and displays capacity constraint violation.                       | Must     | MVP      |
| **ORG-FAIL-011** | Event publication fails due to external service unavailability.                       | System returns error and retains event in `DRAFT` state; organizer may retry.                  | Should   | Advanced |
| **ORG-FAIL-012** | Organizer attempts to assign staff to an event they do not own.                       | System returns authorization error.                                                            | Must     | MVP      |
| **ORG-FAIL-013** | Organizer enters an event date in the past.                                           | System rejects with validation error: "Event date must be in the future."                      | Must     | MVP      |

---

## 7. Security & Privacy (Organizer-Specific)

### 7.1 Account Protection

| Requirement ID  | Requirement                                                                                                                               | Priority | Scope |
| :-------------- | :---------------------------------------------------------------------------------------------------------------------------------------- | :------- | :---- |
| **ORG-SEC-001** | Organizer passwords must be hashed using approved algorithms (consistent with platform security baseline: Argon2id or bcrypt, cost ≥ 12). | Must     | MVP   |
| **ORG-SEC-002** | Organizer sessions must use `HttpOnly`, `SameSite=Strict`, and `Secure` cookie flags.                                                     | Must     | MVP   |
| **ORG-SEC-003** | Organizer session tokens must expire after a reasonable inactivity period. Exact duration is a _OPEN BUSINESS DECISION_.                  | Must     | MVP   |

### 7.2 Authorization

| Requirement ID  | Requirement                                                                                                       | Priority | Scope |
| :-------------- | :---------------------------------------------------------------------------------------------------------------- | :------- | :---- |
| **ORG-SEC-010** | All organizer API routes and dashboard views must enforce `ORGANIZER` role verification.                          | Must     | MVP   |
| **ORG-SEC-011** | Organizer resource access must be scoped to events owned by or explicitly delegated to the organizer (BRULE-008). | Must     | MVP   |
| **ORG-SEC-012** | Cross-organizer data access must be strictly forbidden at the authorization layer.                                | Must     | MVP   |

### 7.3 Attendee Data Privacy

| Requirement ID  | Requirement                                                                                                                             | Priority | Scope      |
| :-------------- | :-------------------------------------------------------------------------------------------------------------------------------------- | :------- | :--------- |
| **ORG-SEC-020** | Organizers must only access attendee information for bookings associated with their own events.                                         | Must     | MVP        |
| **ORG-SEC-021** | Organizer access to attendee data is limited to: attendee name, attendee email, ticket category, booking status, and attendance status. | Must     | MVP        |
| **ORG-SEC-022** | Organizers must not access attendee payment details, passwords, or other private account information.                                   | Must     | MVP        |
| **ORG-SEC-023** | Attendee data export must comply with platform privacy policies. Export functionality approval is an _OPEN BUSINESS DECISION_.          | Should   | Production |

### 7.4 Financial Information

| Requirement ID  | Requirement                                                                                | Priority | Scope |
| :-------------- | :----------------------------------------------------------------------------------------- | :------- | :---- |
| **ORG-SEC-030** | Organizers must see aggregate sales totals, not individual payment instrument details.     | Must     | MVP   |
| **ORG-SEC-031** | Organizers must not access raw payment gateway credentials or transaction-level card data. | Must     | MVP   |

### 7.5 Auditability

| Requirement ID  | Requirement                                                                                                                                   | Priority | Scope      |
| :-------------- | :-------------------------------------------------------------------------------------------------------------------------------------------- | :------- | :--------- |
| **ORG-SEC-040** | Critical organizer actions (event creation, publication, cancellation, capacity changes, staff assignments) must be logged to an audit trail. | Should   | Production |
| **ORG-SEC-041** | Audit records must capture: action type, organizer ID, event ID, timestamp, and change summary.                                               | Should   | Production |

---

## 8. Test Requirements (Future)

### 8.1 Test Categories for Organizer Domain

| Test Area                                | Test Types                                      | Priority Scope                |
| :--------------------------------------- | :---------------------------------------------- | :---------------------------- |
| **Organizer Onboarding**                 | Unit, Integration, API, End-to-End              | MVP                           |
| **Event Creation**                       | Unit, Integration, API, End-to-End, Negative    | MVP                           |
| **Event Editing**                        | Unit, Integration, API, Negative                | MVP                           |
| **Event Publishing**                     | Unit, Integration, API, End-to-End, Negative    | MVP                           |
| **Event Cancellation**                   | Unit, Integration, API, End-to-End              | Advanced                      |
| **Venue Configuration**                  | Unit, Integration, API, Negative                | MVP (GA), Advanced (Reserved) |
| **Inventory Configuration**              | Unit, Integration, API, Negative, Concurrency   | MVP                           |
| **Pricing**                              | Unit, Integration, Negative                     | MVP                           |
| **Access Control (Organizer Isolation)** | Unit, Integration, API, Security, Authorization | MVP                           |
| **Attendee Data Access**                 | Integration, API, Security, Privacy             | MVP                           |
| **Staff Management**                     | Unit, Integration, API, Authorization           | MVP                           |
| **Organizer Dashboard**                  | Integration, End-to-End, Accessibility          | MVP                           |
| **Organizer Notifications**              | Integration, End-to-End                         | Advanced                      |
| **Event Modification After Sales**       | Integration, API, Negative, Concurrency         | MVP                           |

### 8.2 Key Negative Test Scenarios

| Test Scenario                                           | Expected Result                                |
| :------------------------------------------------------ | :--------------------------------------------- |
| Organizer A attempts to edit Organizer B's event.       | 403 Forbidden.                                 |
| Organizer publishes event with missing required fields. | Validation error with field list.              |
| Organizer reduces capacity below sold count.            | Rejection with current sold count.             |
| Organizer sets negative ticket price.                   | Validation error.                              |
| Organizer cancels an already-cancelled event.           | Informational error.                           |
| Organizer assigns staff to event they do not own.       | 403 Forbidden.                                 |
| Organizer accesses attendee payment details.            | 403 Forbidden or data not present in response. |
| Suspended organizer attempts to publish event.          | Authorization error.                           |

---

## 9. MVP vs Advanced vs Production Requirements

### 9.1 MVP Organizer Requirements

The MVP enables an organizer to complete a coherent end-to-end workflow:

**Onboard → Create Event → Configure Tickets/Inventory → Publish → Sell Tickets → View Bookings → Manage Attendees**

| Category            | MVP Requirements                                                                                                            |
| :------------------ | :-------------------------------------------------------------------------------------------------------------------------- |
| **Registration**    | ORG-REG-001 through ORG-REG-004                                                                                             |
| **Profile**         | ORG-PROF-001, ORG-PROF-004                                                                                                  |
| **Account**         | ORG-ACCT-001                                                                                                                |
| **Dashboard**       | ORG-DASH-001 through ORG-DASH-003, ORG-DASH-010 through ORG-DASH-013, ORG-DASH-019                                          |
| **Event Creation**  | See `event-management-requirements.md`                                                                                      |
| **Venue/Inventory** | See `venue-inventory-requirements.md`                                                                                       |
| **Publishing**      | See `event-management-requirements.md`                                                                                      |
| **Notifications**   | ORG-NOTIF-004, ORG-NOTIF-007, ORG-NOTIF-010                                                                                 |
| **Security**        | ORG-SEC-001 through ORG-SEC-003, ORG-SEC-010 through ORG-SEC-012, ORG-SEC-020 through ORG-SEC-022, ORG-SEC-030, ORG-SEC-031 |
| **Access Control**  | See `organizer-access-control.md`                                                                                           |

### 9.2 Advanced Organizer Requirements

| Category             | Advanced Requirements                                                     |
| :------------------- | :------------------------------------------------------------------------ |
| **Registration**     | ORG-REG-005 (Terms of Service acceptance)                                 |
| **Profile**          | ORG-PROF-002, ORG-PROF-003 (profile image, organization info)             |
| **Account**          | ORG-ACCT-002 through ORG-ACCT-004 (suspension, deactivation)              |
| **Dashboard**        | ORG-DASH-014 through ORG-DASH-018 (revenue, activity, alerts, attendance) |
| **Notifications**    | ORG-NOTIF-001 through ORG-NOTIF-003, ORG-NOTIF-006, ORG-NOTIF-011         |
| **Event Management** | Advanced editing, reserved seating, multi-tier pricing                    |
| **Analytics**        | See `event-management-requirements.md` — Advanced analytics               |

### 9.3 Production / Extreme Organizer Requirements

| Category          | Production Requirements                                                      |
| :---------------- | :--------------------------------------------------------------------------- |
| **Registration**  | ORG-REG-006 (identity/business verification)                                 |
| **Notifications** | ORG-NOTIF-005, ORG-NOTIF-012 (low-inventory alerts, preferences)             |
| **Security**      | ORG-SEC-023 (data export compliance), ORG-SEC-040, ORG-SEC-041 (audit trail) |
| **Analytics**     | Production-grade analytics (real-time dashboards, export)                    |

---

## 10. Open Business & Product Decisions

| Decision ID     | Decision Area           | Description                                                                                      | Impact                                                   |
| :-------------- | :---------------------- | :----------------------------------------------------------------------------------------------- | :------------------------------------------------------- |
| **OBD-ORG-001** | Organization Entities   | Whether organizer accounts can belong to a multi-user organization with shared event management. | Affects access control, delegation, and team management. |
| **OBD-ORG-002** | Identity Verification   | Whether organizers require identity or business verification before publishing events.           | Affects onboarding flow and compliance.                  |
| **OBD-ORG-003** | Account Deactivation    | Behaviour of existing published events when an organizer deactivates their account.              | Affects attendee experience and refund policies.         |
| **OBD-ORG-004** | Low-Inventory Threshold | The threshold value and opt-in policy for low-inventory notifications.                           | Affects notification volume and organizer experience.    |
| **OBD-ORG-005** | Session Timeout         | Exact organizer session inactivity timeout duration.                                             | Affects security and usability.                          |
| **OBD-ORG-006** | Attendee Data Export    | Whether organizers may export attendee lists and under what privacy constraints.                 | Affects privacy compliance and attendee trust.           |

---

## 11. Technical Decisions Deferred to Later Phases

| Area                         | Deferred Decision                                      |
| :--------------------------- | :----------------------------------------------------- |
| Database schema              | Organizer table structure, relationships, indexes      |
| API endpoints                | REST/GraphQL route definitions                         |
| UI/UX components             | Dashboard layout, form components, responsive design   |
| Authentication mechanism     | JWT, session cookies, OAuth provider integration       |
| Notification delivery        | Email provider, SMS gateway, push notification service |
| Analytics technology         | Analytics engine, data warehouse, visualization tools  |
| Authorization implementation | Middleware, policy engine, permission checks           |
| Audit logging                | Log storage, log format, retention policies            |

---

## 12. Definition of Done — Organizer Requirements

- [x] Organizer identity and domain model defined.
- [x] Organizer onboarding (registration, profile, account status) defined.
- [x] Organizer dashboard capabilities defined.
- [x] Organizer notifications defined.
- [x] Organizer failure scenarios documented.
- [x] Security and privacy requirements documented.
- [x] Future test requirements identified.
- [x] MVP vs Advanced vs Production separated.
- [x] Open business decisions catalogued.
- [x] No technical implementation prematurely selected.
- [x] Saved to [`docs/requirements/organizer-requirements.md`](docs/requirements/organizer-requirements.md).
