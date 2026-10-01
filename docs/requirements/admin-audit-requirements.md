# EVENTRA — Admin Audit Trail Requirements

---

## 1. Overview & Purpose

This document defines the requirements for the EVENTRA administrative audit trail. The audit trail provides an immutable, append-only record of all privileged administrative actions for accountability, compliance, dispute resolution, and security investigation. Consistent with BR-013 (Super-Admin Governance & Audit Trail).

---

## 2. Audit Record Structure

Every audit trail entry must capture the following fields:

| Field                    | Description                                                                                                         | Required                                                 |
| :----------------------- | :------------------------------------------------------------------------------------------------------------------ | :------------------------------------------------------- |
| **Audit ID**             | Unique identifier for the audit record.                                                                             | Yes                                                      |
| **Timestamp**            | Date and time the action was performed (UTC).                                                                       | Yes                                                      |
| **Actor ID**             | The admin account ID that performed the action.                                                                     | Yes                                                      |
| **Actor Role**           | The admin role at the time of action (`SUPER_ADMIN`, `READ_ONLY_ADMIN`).                                            | Yes                                                      |
| **Action Type**          | The category and name of the action performed (see Section 3).                                                      | Yes                                                      |
| **Target Resource Type** | The type of resource affected (e.g., `customer_account`, `event`, `booking`, `payment`, `ticket`, `admin_account`). | Yes                                                      |
| **Target Resource ID**   | The unique ID of the affected resource.                                                                             | Yes                                                      |
| **Previous State**       | The state of the resource before the action (where applicable).                                                     | Conditional                                              |
| **Resulting State**      | The state of the resource after the action.                                                                         | Yes                                                      |
| **Reason / Context**     | Admin-provided justification or context for the action.                                                             | Conditional (required for destructive/financial actions) |
| **Outcome**              | Whether the action succeeded, failed, or was rejected.                                                              | Yes                                                      |
| **Additional Metadata**  | Action-specific data (e.g., refund amount, number of affected bookings).                                            | Optional                                                 |

---

## 3. Auditable Admin Actions

### 3.1 Admin Account Actions

| Action Type                 | Description                                                   | Reason Required                  | Previous State Captured |
| :-------------------------- | :------------------------------------------------------------ | :------------------------------- | :---------------------- |
| `admin_account_created`     | New admin account created.                                    | No                               | N/A                     |
| `admin_account_suspended`   | Admin account suspended.                                      | Yes                              | `ACTIVE`                |
| `admin_account_reinstated`  | Admin account reinstated from suspension.                     | Yes                              | `SUSPENDED`             |
| `admin_account_deactivated` | Admin account permanently deactivated.                        | Yes                              | `ACTIVE` or `SUSPENDED` |
| `admin_role_modified`       | Admin role changed (e.g., `READ_ONLY_ADMIN` → `SUPER_ADMIN`). | Yes                              | Previous role           |
| `admin_login_success`       | Successful admin login.                                       | No                               | N/A                     |
| `admin_login_failed`        | Failed admin login attempt.                                   | No                               | N/A                     |
| `admin_session_terminated`  | Admin session forcibly terminated.                            | Yes (if forced by another admin) | N/A                     |

### 3.2 Customer Account Actions

| Action Type                   | Description                                                | Reason Required | Previous State Captured |
| :---------------------------- | :--------------------------------------------------------- | :-------------- | :---------------------- |
| `customer_account_suspended`  | Customer account suspended by admin.                       | Yes             | `ACTIVE`                |
| `customer_account_reinstated` | Customer account reinstated by admin.                      | Yes             | `SUSPENDED`             |
| `customer_data_accessed`      | Admin accessed customer account details for investigation. | No              | N/A                     |

### 3.3 Organizer Account Actions

| Action Type                       | Description                             | Reason Required | Previous State Captured |
| :-------------------------------- | :-------------------------------------- | :-------------- | :---------------------- |
| `organizer_account_suspended`     | Organizer account suspended by admin.   | Yes             | `ACTIVE`                |
| `organizer_account_reinstated`    | Organizer account reinstated by admin.  | Yes             | `SUSPENDED`             |
| `organizer_account_deactivated`   | Organizer account deactivated by admin. | Yes             | `ACTIVE` or `SUSPENDED` |
| `organizer_verification_approved` | Organizer verification approved.        | No              | N/A                     |
| `organizer_verification_rejected` | Organizer verification rejected.        | Yes             | N/A                     |

### 3.4 Event Moderation Actions

| Action Type             | Description                                     | Reason Required | Previous State Captured    |
| :---------------------- | :---------------------------------------------- | :-------------- | :------------------------- |
| `event_suspended`       | Event suspended by admin (hidden from catalog). | Yes             | `PUBLISHED`                |
| `event_reinstated`      | Suspended event reinstated by admin.            | Yes             | `SUSPENDED`                |
| `event_cancelled_admin` | Event administratively cancelled by admin.      | Yes             | `PUBLISHED` or `SUSPENDED` |
| `event_flagged`         | Event flagged for moderation review.            | Yes             | N/A                        |

### 3.5 Booking & Order Actions

| Action Type               | Description                                       | Reason Required | Previous State Captured |
| :------------------------ | :------------------------------------------------ | :-------------- | :---------------------- |
| `booking_cancelled_admin` | Booking cancelled by admin.                       | Yes             | `PAID`                  |
| `booking_investigated`    | Admin accessed booking details for investigation. | No              | N/A                     |

### 3.6 Payment & Refund Actions

| Action Type            | Description                                       | Reason Required              | Previous State Captured   |
| :--------------------- | :------------------------------------------------ | :--------------------------- | :------------------------ |
| `refund_initiated`     | Refund initiated by admin.                        | Yes                          | `SUCCESS` (payment state) |
| `refund_completed`     | Refund successfully processed.                    | No                           | `REFUND_PENDING`          |
| `refund_failed`        | Refund attempt failed.                            | No (failure reason captured) | `REFUND_PENDING`          |
| `payment_investigated` | Admin accessed payment details for investigation. | No                           | N/A                       |

### 3.7 Ticket Actions

| Action Type          | Description                                   | Reason Required | Previous State Captured |
| :------------------- | :-------------------------------------------- | :-------------- | :---------------------- |
| `ticket_invalidated` | Ticket invalidated by admin for fraud/policy. | Yes             | `ISSUED`                |

### 3.8 Staff Actions

| Action Type                | Description                               | Reason Required | Previous State Captured |
| :------------------------- | :---------------------------------------- | :-------------- | :---------------------- |
| `staff_assignment_removed` | Staff member removed from event by admin. | Yes             | Assigned                |

### 3.9 Configuration Actions

| Action Type       | Description                                      | Reason Required | Previous State Captured |
| :---------------- | :----------------------------------------------- | :-------------- | :---------------------- |
| `config_modified` | Platform configuration setting changed by admin. | Yes             | Previous value          |

### 3.10 Security Actions

| Action Type                  | Description                                        | Reason Required | Previous State Captured |
| :--------------------------- | :------------------------------------------------- | :-------------- | :---------------------- |
| `security_incident_response` | Security incident response action taken.           | Yes             | N/A                     |
| `session_force_terminated`   | Admin forcibly terminated another user's sessions. | Yes             | N/A                     |

---

## 4. Audit Access Restrictions

| Requirement ID | Requirement                                                                                                   | Priority                                                              | Scope      |
| :------------- | :------------------------------------------------------------------------------------------------------------ | :-------------------------------------------------------------------- | :--------- |
| **AUDIT-001**  | Both `SUPER_ADMIN` and `READ_ONLY_ADMIN` must be able to view audit trail records.                            | Should                                                                | Advanced   |
| **AUDIT-002**  | No admin role, including `SUPER_ADMIN`, may modify or delete audit trail entries. Audit trail is append-only. | Must                                                                  | Production |
| **AUDIT-003**  | Audit trail access must itself be logged (who accessed audit records and when).                               | Could                                                                 | Production |
| **AUDIT-004**  | Audit trail must be searchable by: actor, action type, target resource, date range.                           | Should                                                                | Advanced   |
| **AUDIT-005**  | Audit trail export capability.                                                                                | _OPEN BUSINESS DECISION_ — Export format and access restrictions TBD. | Production |

---

## 5. Retention Requirements

| Requirement ID | Requirement                                                           | Priority                                                                                                                                          | Scope      |
| :------------- | :-------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------ | :--------- |
| **AUDIT-010**  | Audit trail retention period.                                         | _OPEN BUSINESS DECISION_ (OBD-ADM-010) — Retention period not yet established. Factors: regulatory requirements, storage costs, compliance needs. | Production |
| **AUDIT-011**  | Audit records must not be purged before the retention period expires. | Should                                                                                                                                            | Production |
| **AUDIT-012**  | Audit records past retention may be archived rather than deleted.     | _OPEN BUSINESS DECISION_ — Archive vs delete policy TBD.                                                                                          | Production |

---

## 6. Tamper Protection & Immutability

| Requirement ID | Requirement                                                                                                      | Priority                                                                                                                                                                            | Scope      |
| :------------- | :--------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------- |
| **AUDIT-020**  | The audit trail must be append-only. No existing entries may be modified after creation. Consistent with BR-013. | Must                                                                                                                                                                                | Production |
| **AUDIT-021**  | The audit trail must be tamper-resistant. Unauthorized modification or deletion attempts must be detectable.     | Should                                                                                                                                                                              | Production |
| **AUDIT-022**  | Audit trail integrity verification mechanism.                                                                    | _OPEN BUSINESS DECISION_ — Whether cryptographic chaining, checksums, or external audit log services are required. Implementation is a technical decision deferred to later phases. | Production |

---

## 7. Audit Requirements by Action Sensitivity

### 7.1 High-Sensitivity Actions (Audit Always Required)

| Action Category              | Examples                                       | Scope      |
| :--------------------------- | :--------------------------------------------- | :--------- |
| **Admin account management** | Create, suspend, deactivate, role change.      | MVP        |
| **Financial actions**        | Refund initiation, refund completion/failure.  | Advanced   |
| **Event moderation**         | Suspend, reinstate, cancel event.              | Advanced   |
| **Account status changes**   | Customer/organizer suspension, reinstatement.  | Advanced   |
| **Security incidents**       | Incident response, forced session termination. | Advanced   |
| **Configuration changes**    | Platform settings modifications.               | Production |

### 7.2 Medium-Sensitivity Actions (Audit Recommended)

| Action Category          | Examples                            | Scope    |
| :----------------------- | :---------------------------------- | :------- |
| **Booking cancellation** | Admin cancels customer booking.     | Advanced |
| **Ticket invalidation**  | Admin invalidates ticket for fraud. | Advanced |
| **Staff modification**   | Admin removes staff assignment.     | Advanced |

### 7.3 Low-Sensitivity Actions (Audit Optional)

| Action Category       | Examples                                       | Scope      |
| :-------------------- | :--------------------------------------------- | :--------- |
| **Data access**       | Admin views customer details, booking history. | Production |
| **Report generation** | Admin generates or exports reports.            | Production |
| **Search operations** | Admin searches for users, bookings, events.    | Production |

---

## 8. Definition of Done — Audit Requirements

- [x] Audit record structure defined (actor, action, target, timestamp, reason, states, outcome).
- [x] All auditable admin action types catalogued.
- [x] Audit access restrictions defined (view for both admin roles; no modification for any role).
- [x] Retention marked as OPEN decision.
- [x] Tamper protection and immutability requirements defined.
- [x] Actions classified by sensitivity level.
- [x] Consistent with BR-013 and security-requirements-reference.md.
- [x] No technical implementation prematurely selected.
- [x] Saved to `docs/requirements/admin-audit-requirements.md`.
