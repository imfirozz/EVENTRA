# EVENTRA — Admin Requirements Specification

---

## 1. Overview & Purpose

This document defines the complete **Platform Administration Requirements** for EVENTRA. The `SUPER_ADMIN` role holds global platform oversight, user management, event moderation, financial reconciliation, security incident response, and audit capabilities. These requirements define what administrators must be able to do, the rules governing those actions, and the boundaries of administrative authority. No technical implementation decisions (database schemas, API endpoints, backend architecture, infrastructure) are prescribed.

Consistent with FR-004, the platform RBAC defines four roles: `ATTENDEE`, `ORGANIZER`, `GATE_STAFF`, `SUPER_ADMIN`.

---

## 2. Admin Domain Model

### 2.1 Conceptual Entities

| Entity                     | Description                                                                                            | Key Relationships                               |
| :------------------------- | :----------------------------------------------------------------------------------------------------- | :---------------------------------------------- |
| **Admin Account**          | An authenticated user with the `SUPER_ADMIN` role possessing platform-wide governance privileges.      | Has Admin Sessions, performs Admin Actions.     |
| **Admin Role**             | A named permission set governing which administrative actions a user may perform.                      | Assigned to Admin Account.                      |
| **Admin Session**          | An authenticated admin session with elevated security controls.                                        | Belongs to Admin Account.                       |
| **Admin Action**           | A discrete privileged operation performed by an admin, logged to the audit trail.                      | Performed by Admin Account, targets a Resource. |
| **Moderation Action**      | An admin action that changes the visibility, status, or availability of an organizer account or event. | Subset of Admin Action.                         |
| **Customer Account**       | An attendee account managed or investigated by an admin.                                               | Target of Admin Action.                         |
| **Organizer Account**      | An organizer account subject to admin verification, suspension, or investigation.                      | Target of Moderation Action.                    |
| **Event**                  | An event listing subject to admin moderation, suspension, or administrative cancellation.              | Target of Moderation Action.                    |
| **Booking**                | A customer order record subject to admin investigation or refund action.                               | Target of Admin Action.                         |
| **Payment Transaction**    | A financial record subject to admin investigation or refund reconciliation.                            | Associated with Booking.                        |
| **Refund**                 | A financial reversal action triggered or approved by an admin.                                         | Linked to Payment Transaction and Booking.      |
| **Ticket**                 | A digital pass subject to admin investigation or invalidation.                                         | Linked to Booking.                              |
| **Audit Trail**            | An append-only, tamper-resistant log of all privileged admin actions.                                  | Contains Admin Actions.                         |
| **Platform Configuration** | System-level settings managed by admins (e.g., feature flags, notification policies).                  | Managed by Admin Account.                       |

### 2.2 Domain Relationship Summary

```
Admin Account
├── Admin Role (SUPER_ADMIN | READ_ONLY_ADMIN)
├── Admin Session
├── Admin Actions (0..*)
│   ├── Customer Management Actions
│   ├── Organizer Management Actions
│   ├── Event Moderation Actions
│   ├── Booking/Payment/Refund Actions
│   ├── Ticket Administration Actions
│   ├── Staff Administration Actions
│   ├── Security Actions
│   └── Configuration Actions
├── Audit Trail (append-only log of actions)
└── Platform Configuration
```

---

## 3. Admin Roles

### 3.1 Role Definitions

| Role                | Description                                                                                                                                                                       | Scope                   |
| :------------------ | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------- |
| **SUPER_ADMIN**     | Full administrative authority across the platform. Can perform all admin actions including user management, event moderation, financial operations, and security responses.       | MVP (single-tier admin) |
| **READ_ONLY_ADMIN** | Audit/compliance observer with view-only access to admin dashboards, reports, and audit logs. Cannot perform any create, modify, delete, approve, financial, or security actions. | Advanced                |

> **Note**: Additional admin sub-roles (e.g., `FINANCE_ADMIN`, `SUPPORT_ADMIN`, `SECURITY_ADMIN`) are an _OPEN BUSINESS DECISION_ for Production scope. See admin-role-permission-matrix.md for the complete permission matrix.

### 3.2 Admin Onboarding & Account Management

| Requirement ID | Requirement                                                                                                                                                                             | Priority                                                      | Scope      |
| :------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------ | :--------- |
| **ADMIN-001**  | Admin accounts must be created by an existing `SUPER_ADMIN`. Self-registration as admin is forbidden.                                                                                   | Must                                                          | MVP        |
| **ADMIN-002**  | Admin account creation must capture: name, email, assigned admin role, and created-by reference.                                                                                        | Must                                                          | MVP        |
| **ADMIN-003**  | Admin accounts must use the same password security standards as all platform accounts (Argon2id or bcrypt, cost ≥ 12). Consistent with security-requirements-reference.md.              | Must                                                          | MVP        |
| **ADMIN-004**  | New admin accounts must default to `ACTIVE` status.                                                                                                                                     | Must                                                          | MVP        |
| **ADMIN-005**  | A `SUPER_ADMIN` must be able to deactivate another admin account. A `SUPER_ADMIN` must not be able to deactivate their own account if they are the last remaining active `SUPER_ADMIN`. | Must                                                          | MVP        |
| **ADMIN-006**  | A `SUPER_ADMIN` must be able to modify another admin's role assignment.                                                                                                                 | Should                                                        | Advanced   |
| **ADMIN-007**  | Admin privilege modifications must be logged to the audit trail.                                                                                                                        | Must                                                          | MVP        |
| **ADMIN-008**  | Multi-factor authentication (MFA) for admin accounts.                                                                                                                                   | _OPEN BUSINESS DECISION_ — MFA policy for admin accounts TBD. | Production |

### 3.3 Admin Account Status

| Status          | Meaning                                                                                     |
| :-------------- | :------------------------------------------------------------------------------------------ |
| **ACTIVE**      | Admin account is operational; can perform assigned role actions.                            |
| **SUSPENDED**   | Admin account temporarily disabled pending investigation; cannot perform any admin actions. |
| **DEACTIVATED** | Admin account permanently disabled. Terminal state.                                         |

---

## 4. Admin Dashboard & Platform Overview

| Requirement ID | Requirement                                                                                                                             | Priority | Scope    |
| :------------- | :-------------------------------------------------------------------------------------------------------------------------------------- | :------- | :------- |
| **ADMIN-010**  | The admin dashboard must be accessible only to users with `SUPER_ADMIN` or `READ_ONLY_ADMIN` role. Consistent with FR-004, ACC-ADM-001. | Must     | MVP      |
| **ADMIN-011**  | The admin dashboard must display platform-wide summary metrics: total users, total events, total bookings, total revenue.               | Should   | Advanced |
| **ADMIN-012**  | The admin dashboard must provide access to user management, event management, booking management, and audit logs.                       | Must     | MVP      |
| **ADMIN-013**  | The admin dashboard must display recent admin actions and alerts requiring attention.                                                   | Should   | Advanced |

---

## 5. Customer Management

| Requirement ID | Requirement                                                                                                    | Priority | Scope    |
| :------------- | :------------------------------------------------------------------------------------------------------------- | :------- | :------- |
| **ADMIN-020**  | A `SUPER_ADMIN` must be able to view a list of all customer (attendee) accounts.                               | Must     | MVP      |
| **ADMIN-021**  | A `SUPER_ADMIN` must be able to search customers by name, email, or account ID.                                | Must     | MVP      |
| **ADMIN-022**  | A `SUPER_ADMIN` must be able to view customer account details: name, email, account status, registration date. | Must     | MVP      |
| **ADMIN-023**  | A `SUPER_ADMIN` must be able to view a customer's booking history for investigation purposes.                  | Should   | Advanced |
| **ADMIN-024**  | A `SUPER_ADMIN` must be able to suspend a customer account (preventing login and new bookings).                | Should   | Advanced |
| **ADMIN-025**  | A `SUPER_ADMIN` must be able to reinstate a suspended customer account.                                        | Should   | Advanced |
| **ADMIN-026**  | A `SUPER_ADMIN` must not access customer passwords (hashed, never viewable).                                   | Must     | MVP      |
| **ADMIN-027**  | A `SUPER_ADMIN` must not access raw payment card data. Consistent with BRULE-004.                              | Must     | MVP      |
| **ADMIN-028**  | Customer account status changes must be logged to the audit trail with reason.                                 | Should   | Advanced |
| **ADMIN-029**  | A `READ_ONLY_ADMIN` may view customer account details and booking history but must not modify accounts.        | Should   | Advanced |

---

## 6. Organizer Management

| Requirement ID | Requirement                                                                                                 | Priority                                                                             | Scope      |
| :------------- | :---------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------- | :--------- |
| **ADMIN-030**  | A `SUPER_ADMIN` must be able to view a list of all organizer accounts.                                      | Must                                                                                 | MVP        |
| **ADMIN-031**  | A `SUPER_ADMIN` must be able to view organizer account details: name, email, account status, events owned.  | Must                                                                                 | MVP        |
| **ADMIN-032**  | A `SUPER_ADMIN` must be able to suspend an organizer account. Consistent with ORG-ACCT-002, LIFE-ACCT-002.  | Should                                                                               | Advanced   |
| **ADMIN-033**  | A `SUPER_ADMIN` must be able to reinstate a suspended organizer account. Consistent with LIFE-ACCT-003.     | Should                                                                               | Advanced   |
| **ADMIN-034**  | Organizer account suspension must trigger a notification to the organizer. Consistent with ORG-NOTIF-004.   | Must                                                                                 | MVP        |
| **ADMIN-035**  | A `SUPER_ADMIN` must be able to review organizer verification submissions (where verification is required). | _OPEN BUSINESS DECISION_ — Depends on OBD-ORG-002 (organizer identity verification). | Production |
| **ADMIN-036**  | Organizer account status changes must be logged to the audit trail with reason.                             | Should                                                                               | Advanced   |
| **ADMIN-037**  | A `READ_ONLY_ADMIN` may view organizer account details but must not modify accounts.                        | Should                                                                               | Advanced   |

---

## 7. Event Moderation & Governance

| Requirement ID | Requirement                                                                                                                                                                                      | Priority | Scope    |
| :------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------- | :------- |
| **ADMIN-040**  | A `SUPER_ADMIN` must be able to view all events across all organizers. Consistent with BRULE-008 exception for `SUPER_ADMIN`.                                                                    | Must     | MVP      |
| **ADMIN-041**  | A `SUPER_ADMIN` must be able to suspend a published event, transitioning it to `SUSPENDED` state. Consistent with lifecycle-rules.md Event states.                                               | Should   | Advanced |
| **ADMIN-042**  | A suspended event must be hidden from the public catalog.                                                                                                                                        | Should   | Advanced |
| **ADMIN-043**  | A `SUPER_ADMIN` must be able to reinstate a suspended event back to `PUBLISHED`. Consistent with lifecycle-rules.md: `SUSPENDED` → `PUBLISHED` is an allowed transition.                         | Should   | Advanced |
| **ADMIN-044**  | A `SUPER_ADMIN` must be able to administratively cancel an event. Consistent with lifecycle-rules.md: `SUSPENDED` → `CANCELLED` and `PUBLISHED` → `CANCELLED`.                                   | Should   | Advanced |
| **ADMIN-045**  | Administrative event cancellation must follow the same booking/refund/ticket invalidation rules as organizer-initiated cancellation. Consistent with BRULE-010, EVT-CAN-004 through EVT-CAN-007. | Should   | Advanced |
| **ADMIN-046**  | Event moderation actions (suspend, reinstate, cancel) must be logged to the audit trail with reason.                                                                                             | Should   | Advanced |
| **ADMIN-047**  | A `SUPER_ADMIN` must be able to view event details, ticket configuration, inventory, and sales metrics for any event.                                                                            | Must     | MVP      |
| **ADMIN-048**  | A `READ_ONLY_ADMIN` may view event details and moderation history but must not perform moderation actions.                                                                                       | Should   | Advanced |

---

## 8. Venue & Inventory Administration

| Requirement ID | Requirement                                                                                                                                                                                                 | Priority                                                                                  | Scope    |
| :------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------- | :------- |
| **ADMIN-050**  | A `SUPER_ADMIN` must be able to view venue and inventory configuration for any event.                                                                                                                       | Must                                                                                      | MVP      |
| **ADMIN-051**  | A `SUPER_ADMIN` must not directly modify an organizer's venue configuration. Venue configuration remains the organizer's responsibility. Changes by admin require explicit justification and audit logging. | _OPEN BUSINESS DECISION_ — Whether admin can override organizer venue/inventory settings. | Advanced |
| **ADMIN-052**  | Admin inventory overrides, if permitted, must respect BRULE-001 (non-negativity) and BRULE-009 (capacity floor).                                                                                            | Must                                                                                      | MVP      |

---

## 9. Booking & Order Administration

| Requirement ID | Requirement                                                                                                                                                           | Priority | Scope    |
| :------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------- | :------- |
| **ADMIN-060**  | A `SUPER_ADMIN` must be able to search bookings by booking ID, customer email, event name, or transaction ID.                                                         | Should   | Advanced |
| **ADMIN-061**  | A `SUPER_ADMIN` must be able to view full booking details: customer info (name, email), event, ticket category, quantity, booking status, payment status, timestamps. | Should   | Advanced |
| **ADMIN-062**  | A `SUPER_ADMIN` must be able to view booking state transition history for investigation.                                                                              | Should   | Advanced |
| **ADMIN-063**  | A `SUPER_ADMIN` must be able to cancel a confirmed booking and trigger refund reconciliation. Consistent with BRULE-010.                                              | Should   | Advanced |
| **ADMIN-064**  | Admin booking cancellations must restore inventory. Consistent with lifecycle-rules.md: `PAID` → `CANCELLED` restores stock.                                          | Should   | Advanced |
| **ADMIN-065**  | Admin booking modifications must be logged to the audit trail with reason.                                                                                            | Should   | Advanced |
| **ADMIN-066**  | A `READ_ONLY_ADMIN` may view booking details and history but must not cancel or modify bookings.                                                                      | Should   | Advanced |

---

## 10. Payment & Refund Administration

| Requirement ID | Requirement                                                                                                                                                           | Priority                                                                                                           | Scope      |
| :------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------- | :--------- |
| **ADMIN-070**  | A `SUPER_ADMIN` must be able to view payment transaction details for any booking: payment status, gateway transaction ID, amount, timestamps.                         | Should                                                                                                             | Advanced   |
| **ADMIN-071**  | A `SUPER_ADMIN` must not access raw credit card numbers or CVVs. Consistent with BRULE-004.                                                                           | Must                                                                                                               | MVP        |
| **ADMIN-072**  | A `SUPER_ADMIN` must be able to initiate a refund for a confirmed (`PAID`) booking. Refund triggers: `PAID` → `CANCELLED` booking transition and ticket invalidation. | Should                                                                                                             | Advanced   |
| **ADMIN-073**  | Refund actions must be logged to the audit trail with reason, amount, and booking reference.                                                                          | Should                                                                                                             | Advanced   |
| **ADMIN-074**  | Separation of duties for high-value refunds.                                                                                                                          | _OPEN BUSINESS DECISION_ — Whether refunds above a threshold require a second admin approval. Threshold value TBD. | Production |
| **ADMIN-075**  | A `SUPER_ADMIN` must be able to investigate payment discrepancies (e.g., late webhook arrivals, payment mismatches). Consistent with REQ-PAY-005.                     | Should                                                                                                             | Advanced   |
| **ADMIN-076**  | A `READ_ONLY_ADMIN` may view payment details and refund history but must not initiate refunds.                                                                        | Should                                                                                                             | Advanced   |

---

## 11. Ticket Administration

| Requirement ID | Requirement                                                                                                                      | Priority | Scope    |
| :------------- | :------------------------------------------------------------------------------------------------------------------------------- | :------- | :------- |
| **ADMIN-080**  | A `SUPER_ADMIN` must be able to view ticket details for any booking: ticket ID, ticket status, event, attendee, check-in status. | Should   | Advanced |
| **ADMIN-081**  | A `SUPER_ADMIN` must be able to invalidate a ticket (transition to `CANCELLED`) for fraud or policy violation.                   | Should   | Advanced |
| **ADMIN-082**  | Ticket invalidation by admin must be logged to the audit trail with reason.                                                      | Should   | Advanced |
| **ADMIN-083**  | A `READ_ONLY_ADMIN` may view ticket details but must not invalidate tickets.                                                     | Should   | Advanced |

---

## 12. Event Staff Administration

| Requirement ID | Requirement                                                                                              | Priority | Scope    |
| :------------- | :------------------------------------------------------------------------------------------------------- | :------- | :------- |
| **ADMIN-090**  | A `SUPER_ADMIN` must be able to view staff assignments across all events.                                | Should   | Advanced |
| **ADMIN-091**  | A `SUPER_ADMIN` must be able to remove a staff assignment from any event for policy or security reasons. | Should   | Advanced |
| **ADMIN-092**  | Staff assignment changes by admin must be logged to the audit trail.                                     | Should   | Advanced |
| **ADMIN-093**  | A `READ_ONLY_ADMIN` may view staff assignments but must not modify them.                                 | Should   | Advanced |

---

## 13. Platform Moderation

| Requirement ID | Requirement                                                                                                                                       | Priority                                                                    | Scope      |
| :------------- | :------------------------------------------------------------------------------------------------------------------------------------------------ | :-------------------------------------------------------------------------- | :--------- |
| **ADMIN-100**  | A `SUPER_ADMIN` must be able to flag events or organizer accounts for review based on policy violations or user reports.                          | Should                                                                      | Advanced   |
| **ADMIN-101**  | Moderation actions must follow the event lifecycle: `PUBLISHED` → `SUSPENDED` → (`PUBLISHED` or `CANCELLED`). Consistent with lifecycle-rules.md. | Should                                                                      | Advanced   |
| **ADMIN-102**  | Content moderation policies (prohibited content types, violation categories).                                                                     | _OPEN BUSINESS DECISION_ — Specific moderation policies and categories TBD. | Production |
| **ADMIN-103**  | User reporting mechanism (attendees/organizers reporting events or users).                                                                        | _OPEN BUSINESS DECISION_ — Whether user-initiated reports are supported.    | Production |

---

## 14. Support & Customer Service

| Requirement ID | Requirement                                                                                                                                        | Priority | Scope    |
| :------------- | :------------------------------------------------------------------------------------------------------------------------------------------------- | :------- | :------- |
| **ADMIN-110**  | A `SUPER_ADMIN` must be able to look up any booking by transaction ID or customer email for support investigation. Consistent with BR-017, FR-023. | Should   | Advanced |
| **ADMIN-111**  | A `SUPER_ADMIN` must be able to verify ticket validity status for customer disputes.                                                               | Should   | Advanced |
| **ADMIN-112**  | A `SUPER_ADMIN` must be able to process authorized refunds for customer support resolution.                                                        | Should   | Advanced |
| **ADMIN-113**  | Support actions must be logged to the audit trail.                                                                                                 | Should   | Advanced |
| **ADMIN-114**  | A `READ_ONLY_ADMIN` may view support-related booking/ticket information but must not perform support actions.                                      | Should   | Advanced |

---

## 15. Reporting & Analytics

| Requirement ID | Requirement                                                                                                           | Priority                                                      | Scope      |
| :------------- | :-------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------ | :--------- |
| **ADMIN-120**  | A `SUPER_ADMIN` must be able to view platform-wide reports: total users, total events, total bookings, total revenue. | Should                                                        | Advanced   |
| **ADMIN-121**  | A `SUPER_ADMIN` must be able to view event-level reports: sales by event, attendance rates, cancellation rates.       | Should                                                        | Advanced   |
| **ADMIN-122**  | A `SUPER_ADMIN` must be able to view financial reports: total transaction volume, refund volume.                      | Should                                                        | Advanced   |
| **ADMIN-123**  | A `READ_ONLY_ADMIN` must be able to view all reports and analytics.                                                   | Should                                                        | Advanced   |
| **ADMIN-124**  | Report export functionality (CSV/PDF).                                                                                | _OPEN BUSINESS DECISION_ — Export formats and data scope TBD. | Production |
| **ADMIN-125**  | Real-time analytics dashboards.                                                                                       | Could                                                         | Production |

---

## 16. System Configuration

| Requirement ID | Requirement                                                                                                                        | Priority                                                                                                                                          | Scope      |
| :------------- | :--------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------ | :--------- |
| **ADMIN-130**  | A `SUPER_ADMIN` must be able to manage platform-level configuration settings (e.g., reservation hold duration, maximum order cap). | _OPEN BUSINESS DECISION_ — Which settings are admin-configurable vs hardcoded. Current defaults: 300s hold (BRULE-002), 6-ticket cap (BRULE-003). | Production |
| **ADMIN-131**  | Configuration changes must be logged to the audit trail with previous and new values.                                              | Should                                                                                                                                            | Production |
| **ADMIN-132**  | A `READ_ONLY_ADMIN` may view configuration settings but must not modify them.                                                      | Should                                                                                                                                            | Production |

---

## 17. Notification Administration

| Requirement ID | Requirement                                                                                            | Priority | Scope      |
| :------------- | :----------------------------------------------------------------------------------------------------- | :------- | :--------- |
| **ADMIN-140**  | A `SUPER_ADMIN` must be able to trigger platform-wide notifications (e.g., maintenance announcements). | Could    | Production |
| **ADMIN-141**  | A `SUPER_ADMIN` must be able to view notification delivery status and failures.                        | Could    | Production |
| **ADMIN-142**  | A `READ_ONLY_ADMIN` may view notification logs but must not trigger notifications.                     | Could    | Production |

---

## 18. Admin Search

| Requirement ID | Requirement                                                                                                      | Priority | Scope    |
| :------------- | :--------------------------------------------------------------------------------------------------------------- | :------- | :------- |
| **ADMIN-150**  | The admin dashboard must provide a unified search capability across users, events, bookings, and transactions.   | Should   | Advanced |
| **ADMIN-151**  | Search results must respect role permissions (a `READ_ONLY_ADMIN` sees results but cannot take actions on them). | Should   | Advanced |

---

## 19. Error & Failure Handling

| Requirement ID | Failure Scenario                                                                              | Expected Behaviour                                                          | Priority | Scope    |
| :------------- | :-------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------- | :------- | :------- |
| **ADMIN-160**  | Admin attempts to suspend the last remaining active `SUPER_ADMIN`.                            | System rejects action: "Cannot deactivate the last active Super Admin."     | Must     | MVP      |
| **ADMIN-161**  | Admin attempts to cancel an already-cancelled event.                                          | System returns informational error.                                         | Must     | MVP      |
| **ADMIN-162**  | Admin attempts to refund an already-refunded booking.                                         | System rejects duplicate refund.                                            | Should   | Advanced |
| **ADMIN-163**  | Admin attempts to suspend a `DEACTIVATED` organizer account.                                  | System returns error: "Account is already deactivated."                     | Should   | Advanced |
| **ADMIN-164**  | Admin action fails due to external service unavailability (e.g., payment gateway for refund). | System returns error, logs failure, retains current state. Admin may retry. | Should   | Advanced |
| **ADMIN-165**  | Admin submits invalid search criteria.                                                        | System returns validation error with guidance.                              | Must     | MVP      |
| **ADMIN-166**  | Concurrent admin actions on the same resource (e.g., two admins cancelling the same event).   | System applies first action, rejects second with conflict error.            | Should   | Advanced |

---

## 20. Concurrency & Consistency

| Requirement ID | Requirement                                                                                                                   | Priority | Scope    |
| :------------- | :---------------------------------------------------------------------------------------------------------------------------- | :------- | :------- |
| **ADMIN-170**  | Admin booking cancellations and refunds must not violate the inventory non-negativity invariant (BRULE-001).                  | Must     | MVP      |
| **ADMIN-171**  | Admin event cancellations must follow the same booking/inventory reconciliation rules as organizer cancellations (BRULE-010). | Should   | Advanced |
| **ADMIN-172**  | Concurrent admin operations on the same resource must be handled safely (optimistic concurrency or serialized execution).     | Should   | Advanced |

---

## 21. Privacy & Data Protection

| Requirement ID | Requirement                                                                                                                                | Priority                                                      | Scope      |
| :------------- | :----------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------ | :--------- |
| **ADMIN-180**  | Admins must not access customer or organizer passwords. Passwords are hashed and never viewable.                                           | Must                                                          | MVP        |
| **ADMIN-181**  | Admins must not access raw credit card numbers, CVVs, or cardholder data. Consistent with BRULE-004.                                       | Must                                                          | MVP        |
| **ADMIN-182**  | Admin access to PII (names, emails, booking details) must be limited to what is necessary for the administrative function being performed. | Must                                                          | MVP        |
| **ADMIN-183**  | Admin data access must be auditable — every access to sensitive data should be logged.                                                     | Should                                                        | Production |
| **ADMIN-184**  | Data anonymization or erasure requests must be handleable by admin.                                                                        | _OPEN BUSINESS DECISION_ — GDPR/privacy erasure workflow TBD. | Production |

---

## 22. Accessibility & Usability

| Requirement ID | Requirement                                                                                                   | Priority | Scope    |
| :------------- | :------------------------------------------------------------------------------------------------------------ | :------- | :------- |
| **ADMIN-190**  | The admin dashboard must meet the same accessibility standards as the public-facing platform.                 | Should   | Advanced |
| **ADMIN-191**  | Admin workflows must include confirmation prompts for destructive actions (cancellation, suspension, refund). | Must     | MVP      |

---

## 23. MVP vs Advanced vs Production

### 23.1 MVP Admin Requirements

The MVP provides essential admin capabilities for a single `SUPER_ADMIN`:

| Category             | MVP Requirements                                  |
| :------------------- | :------------------------------------------------ |
| **Account**          | ADMIN-001 through ADMIN-005, ADMIN-007            |
| **Dashboard**        | ADMIN-010, ADMIN-012                              |
| **Customer Mgmt**    | ADMIN-020 through ADMIN-022, ADMIN-026, ADMIN-027 |
| **Organizer Mgmt**   | ADMIN-030, ADMIN-031, ADMIN-034                   |
| **Event Governance** | ADMIN-040, ADMIN-047                              |
| **Venue/Inventory**  | ADMIN-050, ADMIN-052                              |
| **Privacy**          | ADMIN-180 through ADMIN-182                       |
| **Errors**           | ADMIN-160, ADMIN-161, ADMIN-165                   |
| **Concurrency**      | ADMIN-170                                         |
| **Usability**        | ADMIN-191                                         |

### 23.2 Advanced Admin Requirements

| Category          | Advanced Requirements                                 |
| :---------------- | :---------------------------------------------------- |
| **Roles**         | READ_ONLY_ADMIN role (ADMIN-006)                      |
| **Dashboard**     | ADMIN-011, ADMIN-013                                  |
| **Customer**      | ADMIN-023 through ADMIN-025, ADMIN-028, ADMIN-029     |
| **Organizer**     | ADMIN-032, ADMIN-033, ADMIN-036, ADMIN-037            |
| **Events**        | ADMIN-041 through ADMIN-046, ADMIN-048                |
| **Bookings**      | ADMIN-060 through ADMIN-066                           |
| **Payments**      | ADMIN-070, ADMIN-072, ADMIN-073, ADMIN-075, ADMIN-076 |
| **Tickets**       | ADMIN-080 through ADMIN-083                           |
| **Staff**         | ADMIN-090 through ADMIN-093                           |
| **Moderation**    | ADMIN-100, ADMIN-101                                  |
| **Support**       | ADMIN-110 through ADMIN-114                           |
| **Reporting**     | ADMIN-120 through ADMIN-123                           |
| **Search**        | ADMIN-150, ADMIN-151                                  |
| **Errors**        | ADMIN-162 through ADMIN-166                           |
| **Concurrency**   | ADMIN-171, ADMIN-172                                  |
| **Accessibility** | ADMIN-190                                             |

### 23.3 Production Admin Requirements

| Category          | Production Requirements                                 |
| :---------------- | :------------------------------------------------------ |
| **Auth**          | ADMIN-008 (MFA)                                         |
| **Organizer**     | ADMIN-035 (verification review)                         |
| **Venue**         | ADMIN-051 (admin override policy)                       |
| **Payments**      | ADMIN-074 (separation of duties for high-value refunds) |
| **Moderation**    | ADMIN-102, ADMIN-103                                    |
| **Reporting**     | ADMIN-124, ADMIN-125                                    |
| **Config**        | ADMIN-130 through ADMIN-132                             |
| **Notifications** | ADMIN-140 through ADMIN-142                             |
| **Privacy**       | ADMIN-183, ADMIN-184                                    |

---

## 24. Open Business & Product Decisions

| Decision ID     | Decision Area               | Description                                                                                                                    |
| :-------------- | :-------------------------- | :----------------------------------------------------------------------------------------------------------------------------- |
| **OBD-ADM-001** | Admin Sub-Roles             | Whether additional admin roles beyond `SUPER_ADMIN` and `READ_ONLY_ADMIN` are needed (e.g., `FINANCE_ADMIN`, `SUPPORT_ADMIN`). |
| **OBD-ADM-002** | Admin MFA                   | Whether MFA is required for all admin accounts or only for specific actions.                                                   |
| **OBD-ADM-003** | Admin Venue Override        | Whether admins can directly modify organizer venue/inventory settings.                                                         |
| **OBD-ADM-004** | High-Value Refund Threshold | Whether refunds above a monetary threshold require a second admin approval. Threshold value TBD.                               |
| **OBD-ADM-005** | Content Moderation Policy   | Specific prohibited content categories and violation classification.                                                           |
| **OBD-ADM-006** | User Reporting              | Whether user-initiated reports (attendee/organizer flagging content) are supported.                                            |
| **OBD-ADM-007** | Admin-Configurable Settings | Which platform settings are admin-configurable vs hardcoded.                                                                   |
| **OBD-ADM-008** | Report Export               | Export formats and data scope for admin reports.                                                                               |
| **OBD-ADM-009** | Data Erasure                | GDPR/privacy erasure workflow and admin responsibilities.                                                                      |
| **OBD-ADM-010** | Audit Log Retention         | Retention period for audit trail records.                                                                                      |

---

## 25. Definition of Done — Admin Requirements

- [x] Admin domain model defined.
- [x] Admin roles defined (SUPER_ADMIN, READ_ONLY_ADMIN).
- [x] Admin onboarding and account management defined.
- [x] Customer management requirements defined.
- [x] Organizer management requirements defined.
- [x] Event moderation/governance requirements defined.
- [x] Venue/inventory administration defined.
- [x] Booking/order administration defined.
- [x] Payment/refund administration defined.
- [x] Ticket administration defined.
- [x] Event staff administration defined.
- [x] Platform moderation defined.
- [x] Support/customer service defined.
- [x] Reporting/analytics defined.
- [x] System configuration defined.
- [x] Notification administration defined.
- [x] Admin search defined.
- [x] Error/failure handling defined.
- [x] Concurrency/consistency requirements defined.
- [x] Privacy/data protection requirements defined.
- [x] Accessibility/usability defined.
- [x] MVP / Advanced / Production scope separated.
- [x] Open business decisions catalogued.
- [x] No technical implementation prematurely selected.
- [x] Saved to `docs/requirements/admin-requirements.md`.
