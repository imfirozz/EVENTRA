# EVENTRA — Admin Workflows Specification

---

## 1. Overview & Purpose

This document defines the major administrative workflows for EVENTRA. Each workflow specifies the actor, trigger, preconditions, main flow, alternative flows, exception/failure flows, resulting state, authorization requirements, and audit requirements.

---

## 2. Workflow Catalogue

---

### WF-ADM-001: Admin Onboarding

- **Actor**: Existing `SUPER_ADMIN`
- **Trigger**: Platform requires a new admin account.
- **Preconditions**: Acting admin has an `ACTIVE` `SUPER_ADMIN` account and valid session.
- **Main Flow**:
  1. Acting admin navigates to admin account management.
  2. Acting admin enters new admin details: name, email, assigned role (`SUPER_ADMIN` or `READ_ONLY_ADMIN`).
  3. System validates email uniqueness and format.
  4. System creates admin account with `ACTIVE` status.
  5. System records `created_by` reference to acting admin.
  6. System logs action to audit trail.
  7. System confirms account creation.
- **Alternative Flows**: None for MVP.
- **Exception/Failure Flows**:
  - _Duplicate email_: System rejects with "Email already registered."
  - _Invalid data_: System returns validation errors.
- **Resulting State**: New admin account in `ACTIVE` state.
- **Authorization**: `SUPER_ADMIN` only. `READ_ONLY_ADMIN` cannot create accounts.
- **Audit Requirements**: Log actor, action (admin_account_created), target (new admin ID), role assigned, timestamp.

---

### WF-ADM-002: Customer Investigation

- **Actor**: `SUPER_ADMIN`
- **Trigger**: Support request, suspicious activity alert, or dispute investigation.
- **Preconditions**: Admin has `ACTIVE` `SUPER_ADMIN` session. Customer account exists.
- **Main Flow**:
  1. Admin searches for customer by name, email, or account ID (ADMIN-021).
  2. System returns matching customer records.
  3. Admin views customer account details (ADMIN-022).
  4. Admin views customer booking history (ADMIN-023).
  5. Admin reviews booking states, payment statuses, and ticket statuses.
  6. Admin determines resolution: no action, account suspension, booking cancellation/refund.
- **Alternative Flows**:
  - Admin suspends customer account (ADMIN-024).
  - Admin cancels a specific booking and triggers refund (ADMIN-063, ADMIN-072).
- **Exception/Failure Flows**:
  - _Customer not found_: System returns "No matching customer found."
  - _Account already suspended_: System returns informational message.
- **Resulting State**: Depends on resolution — customer account unchanged, suspended, or booking cancelled.
- **Authorization**: `SUPER_ADMIN` only. `READ_ONLY_ADMIN` may view but not act.
- **Audit Requirements**: Log every access to customer data and any status changes with reason.

---

### WF-ADM-003: Organizer Verification Review

- **Actor**: `SUPER_ADMIN`
- **Trigger**: Organizer submits verification documentation.
- **Preconditions**: Organizer verification is required (_OPEN BUSINESS DECISION_ — OBD-ORG-002). Organizer has submitted verification materials.
- **Main Flow**:
  1. Admin navigates to organizer verification queue.
  2. Admin reviews submitted documentation.
  3. Admin approves or rejects verification.
  4. System updates organizer verification status.
  5. System notifies organizer of outcome.
  6. System logs action to audit trail.
- **Alternative Flows**:
  - Admin requests additional documentation from organizer.
- **Exception/Failure Flows**:
  - _Verification already processed_: System returns informational message.
- **Resulting State**: Organizer verification status updated (approved/rejected/pending additional info).
- **Authorization**: `SUPER_ADMIN` only.
- **Audit Requirements**: Log actor, action, organizer ID, verification decision, timestamp.

> **Note**: This workflow depends on OBD-ORG-002. If organizer verification is not required, this workflow is deferred.

---

### WF-ADM-004: Event Moderation (Suspension)

- **Actor**: `SUPER_ADMIN`
- **Trigger**: Policy violation report, user complaint, or proactive content review.
- **Preconditions**: Event exists in `PUBLISHED` state.
- **Main Flow**:
  1. Admin identifies event requiring moderation.
  2. Admin reviews event details, organizer information, and any reports.
  3. Admin decides to suspend the event.
  4. System prompts admin for confirmation and reason (ADMIN-191).
  5. Admin confirms suspension with documented reason.
  6. System transitions event to `SUSPENDED` state (lifecycle-rules.md).
  7. System removes event from public catalog (ADMIN-042).
  8. System notifies organizer of suspension (ORG-NOTIF-004).
  9. System logs action to audit trail with reason (ADMIN-046).
- **Alternative Flows**:
  - Admin determines no violation; no action taken. Moderation case resolved.
- **Exception/Failure Flows**:
  - _Event already suspended_: System returns "Event is already suspended."
  - _Event already cancelled/completed_: System returns "Event cannot be suspended in its current state."
- **Resulting State**: Event in `SUSPENDED` state, hidden from public catalog.
- **Authorization**: `SUPER_ADMIN` only.
- **Audit Requirements**: Log actor, action (event_suspended), event ID, reason, previous state, resulting state, timestamp.

---

### WF-ADM-005: Event Reinstatement

- **Actor**: `SUPER_ADMIN`
- **Trigger**: Investigation resolved; event cleared for republication.
- **Preconditions**: Event exists in `SUSPENDED` state.
- **Main Flow**:
  1. Admin reviews suspended event and investigation outcome.
  2. Admin decides to reinstate event.
  3. System prompts admin for confirmation.
  4. Admin confirms reinstatement.
  5. System transitions event from `SUSPENDED` → `PUBLISHED` (lifecycle-rules.md).
  6. System restores event visibility in public catalog.
  7. System notifies organizer of reinstatement.
  8. System logs action to audit trail.
- **Alternative Flows**: None.
- **Exception/Failure Flows**:
  - _Event not in SUSPENDED state_: System returns error.
- **Resulting State**: Event back in `PUBLISHED` state, visible in catalog.
- **Authorization**: `SUPER_ADMIN` only.
- **Audit Requirements**: Log actor, action (event_reinstated), event ID, reason, timestamp.

---

### WF-ADM-006: Administrative Event Cancellation

- **Actor**: `SUPER_ADMIN`
- **Trigger**: Severe policy violation, legal requirement, or organizer account deactivation.
- **Preconditions**: Event exists in `PUBLISHED` or `SUSPENDED` state. Consistent with lifecycle-rules.md allowed transitions.
- **Main Flow**:
  1. Admin identifies event requiring cancellation.
  2. Admin reviews event, bookings, and financial impact.
  3. System prompts admin for confirmation and reason (ADMIN-191).
  4. Admin confirms cancellation with documented reason.
  5. System transitions event to `CANCELLED` state (terminal).
  6. System releases all active reservation holds; inventory restored.
  7. System flags all confirmed (`PAID`) bookings for cancellation and refund reconciliation (BRULE-010).
  8. System invalidates all issued tickets (ticket state → `CANCELLED`).
  9. System notifies affected attendees of cancellation (Advanced).
  10. System notifies organizer of administrative cancellation.
  11. System logs action to audit trail with reason.
- **Alternative Flows**: None. Cancellation is final.
- **Exception/Failure Flows**:
  - _Event already cancelled_: System returns "Event is already cancelled." (ADMIN-161).
  - _Refund processing failure_: System logs failure; bookings remain flagged for retry.
- **Resulting State**: Event `CANCELLED`, bookings flagged for refund, tickets invalidated, inventory restored.
- **Authorization**: `SUPER_ADMIN` only.
- **Audit Requirements**: Log actor, action (event_cancelled_admin), event ID, reason, bookings affected count, refund total, timestamp.

---

### WF-ADM-007: Booking Investigation & Cancellation

- **Actor**: `SUPER_ADMIN`
- **Trigger**: Customer support request, fraud suspicion, or payment dispute.
- **Preconditions**: Booking exists.
- **Main Flow**:
  1. Admin searches for booking by booking ID, customer email, or transaction ID (ADMIN-060).
  2. Admin views booking details and state history (ADMIN-061, ADMIN-062).
  3. Admin reviews associated payment and ticket records.
  4. Admin determines resolution.
  5. If cancellation required: admin initiates booking cancellation (ADMIN-063).
  6. System transitions booking from `PAID` → `CANCELLED`.
  7. System restores inventory (BRULE-001).
  8. System triggers refund (ADMIN-072).
  9. System invalidates associated tickets.
  10. System logs action to audit trail (ADMIN-065).
- **Alternative Flows**:
  - No action required; investigation concluded without changes.
- **Exception/Failure Flows**:
  - _Booking not in PAID state_: System rejects cancellation.
  - _Booking already cancelled_: System returns informational message.
- **Resulting State**: Booking `CANCELLED`, inventory restored, refund initiated, tickets invalidated.
- **Authorization**: `SUPER_ADMIN` only. `READ_ONLY_ADMIN` may view but not act.
- **Audit Requirements**: Log actor, action, booking ID, reason, financial impact, timestamp.

---

### WF-ADM-008: Payment Investigation

- **Actor**: `SUPER_ADMIN`
- **Trigger**: Payment discrepancy, late webhook, payment mismatch, or customer dispute.
- **Preconditions**: Payment transaction record exists.
- **Main Flow**:
  1. Admin locates payment transaction via booking search or transaction ID.
  2. Admin reviews payment state, gateway transaction ID, timestamps, and amount.
  3. Admin identifies discrepancy type (late webhook, mismatch, duplicate).
  4. Admin determines resolution.
  5. If refund required: admin initiates refund (ADMIN-072).
  6. System logs investigation and outcome to audit trail.
- **Alternative Flows**:
  - Payment discrepancy resolved without financial action.
- **Exception/Failure Flows**:
  - _Gateway unavailable for refund_: System logs failure; admin may retry (ADMIN-164).
- **Resulting State**: Depends on resolution — payment unchanged or refund initiated.
- **Authorization**: `SUPER_ADMIN` only. `READ_ONLY_ADMIN` may view payment details.
- **Audit Requirements**: Log actor, investigation details, resolution, financial actions taken, timestamp.

---

### WF-ADM-009: Refund Processing

- **Actor**: `SUPER_ADMIN`
- **Trigger**: Booking cancellation (admin or organizer), event cancellation, or customer support resolution.
- **Preconditions**: Booking in `PAID` state with `SUCCESS` payment. Consistent with payment-state-machine.md: `SUCCESS` → `REFUND_PENDING`.
- **Main Flow**:
  1. Admin initiates refund for identified booking (ADMIN-072).
  2. System prompts admin for confirmation, displays refund amount and booking details.
  3. Admin confirms refund with reason.
  4. System transitions payment to `REFUND_PENDING`.
  5. System submits refund request to payment gateway.
  6. Gateway processes and confirms refund.
  7. System transitions payment to `REFUNDED`.
  8. System transitions booking to `CANCELLED`.
  9. System invalidates associated tickets.
  10. System restores inventory.
  11. System logs refund to audit trail with reason, amount, and references.
- **Alternative Flows**:
  - High-value refund requiring second admin approval (_OPEN BUSINESS DECISION_ — OBD-ADM-004).
- **Exception/Failure Flows**:
  - _Already refunded_: System rejects duplicate refund (ADMIN-162).
  - _Gateway rejection_: System logs failure; payment remains in `REFUND_PENDING`.
- **Resulting State**: Payment `REFUNDED`, booking `CANCELLED`, tickets `CANCELLED`, inventory restored.
- **Authorization**: `SUPER_ADMIN` only. `READ_ONLY_ADMIN` may view but not initiate.
- **Audit Requirements**: Log actor, action (refund_initiated), booking ID, amount, reason, gateway response, timestamp.

---

### WF-ADM-010: Ticket Investigation

- **Actor**: `SUPER_ADMIN`
- **Trigger**: Fraud suspicion, duplicate ticket report, or support query.
- **Preconditions**: Ticket record exists.
- **Main Flow**:
  1. Admin locates ticket via booking search or ticket ID.
  2. Admin reviews ticket status, check-in history, and associated booking.
  3. Admin determines if invalidation is required.
  4. If fraud confirmed: admin invalidates ticket (ADMIN-081) — ticket transitions to `CANCELLED`.
  5. System logs invalidation to audit trail with reason (ADMIN-082).
- **Alternative Flows**:
  - No fraud found; ticket remains valid.
- **Exception/Failure Flows**:
  - _Ticket already cancelled_: System returns informational message.
  - _Ticket already checked in_: Admin cannot reverse check-in (consistent with domain-rules.md post-scan rule).
- **Resulting State**: Ticket unchanged or `CANCELLED`.
- **Authorization**: `SUPER_ADMIN` only.
- **Audit Requirements**: Log actor, action, ticket ID, reason, previous state, timestamp.

---

### WF-ADM-011: Security Incident Response

- **Actor**: `SUPER_ADMIN`
- **Trigger**: Detected security breach, compromised account, or suspicious activity pattern.
- **Preconditions**: Security event detected.
- **Main Flow**:
  1. Admin identifies security incident (compromised account, brute force, unauthorized access).
  2. Admin suspends affected account(s) immediately.
  3. System invalidates all active sessions for affected account(s).
  4. Admin investigates scope of compromise.
  5. Admin determines remediation actions (password reset, further suspensions, data review).
  6. Admin documents incident and resolution.
  7. System logs all actions to audit trail.
- **Alternative Flows**:
  - _Compromised admin account_: Another SUPER_ADMIN suspends the compromised admin account and forces all session terminations.
- **Exception/Failure Flows**:
  - _Only one SUPER_ADMIN exists and is compromised_: Emergency recovery procedure required (_OPEN BUSINESS DECISION_ — emergency access recovery not yet defined).
- **Resulting State**: Affected accounts suspended; sessions terminated; incident documented.
- **Authorization**: `SUPER_ADMIN` only.
- **Audit Requirements**: Log all incident response actions with timestamps, affected accounts, and resolution.

---

### WF-ADM-012: Admin Privilege Modification

- **Actor**: `SUPER_ADMIN`
- **Trigger**: Role change requirement (promotion, demotion, or role reassignment).
- **Preconditions**: Target admin account exists and is `ACTIVE` or `SUSPENDED`.
- **Main Flow**:
  1. Acting admin navigates to admin account management.
  2. Acting admin selects target admin account.
  3. Acting admin modifies role assignment (e.g., `READ_ONLY_ADMIN` → `SUPER_ADMIN` or vice versa).
  4. System prompts for confirmation.
  5. Admin confirms change.
  6. System updates role assignment.
  7. System logs change to audit trail (ADMIN-007).
  8. Existing sessions for target admin are invalidated to force re-authentication with new role.
- **Alternative Flows**: None.
- **Exception/Failure Flows**:
  - _Demoting last SUPER_ADMIN to READ_ONLY_ADMIN_: System rejects — platform must always have at least one active SUPER_ADMIN (ADMIN-005).
  - _Modifying own role_: _OPEN BUSINESS DECISION_ — Whether an admin can modify their own role.
- **Resulting State**: Admin role updated; sessions invalidated.
- **Authorization**: `SUPER_ADMIN` only.
- **Audit Requirements**: Log actor, action (admin_role_modified), target admin ID, previous role, new role, timestamp.

---

## 3. Workflow Summary Matrix

| Workflow ID | Name                                 | Actor       | Scope               |
| :---------- | :----------------------------------- | :---------- | :------------------ |
| WF-ADM-001  | Admin Onboarding                     | SUPER_ADMIN | MVP                 |
| WF-ADM-002  | Customer Investigation               | SUPER_ADMIN | Advanced            |
| WF-ADM-003  | Organizer Verification Review        | SUPER_ADMIN | Production (_OPEN_) |
| WF-ADM-004  | Event Moderation (Suspension)        | SUPER_ADMIN | Advanced            |
| WF-ADM-005  | Event Reinstatement                  | SUPER_ADMIN | Advanced            |
| WF-ADM-006  | Administrative Event Cancellation    | SUPER_ADMIN | Advanced            |
| WF-ADM-007  | Booking Investigation & Cancellation | SUPER_ADMIN | Advanced            |
| WF-ADM-008  | Payment Investigation                | SUPER_ADMIN | Advanced            |
| WF-ADM-009  | Refund Processing                    | SUPER_ADMIN | Advanced            |
| WF-ADM-010  | Ticket Investigation                 | SUPER_ADMIN | Advanced            |
| WF-ADM-011  | Security Incident Response           | SUPER_ADMIN | Advanced            |
| WF-ADM-012  | Admin Privilege Modification         | SUPER_ADMIN | Advanced            |

---

## 4. Definition of Done — Admin Workflows

- [x] 12 admin workflows fully specified with actor, trigger, preconditions, flows, authorization, and audit.
- [x] All workflows consistent with lifecycle-rules.md, BRULE-001, BRULE-004, BRULE-010.
- [x] Destructive actions require confirmation prompts (ADMIN-191).
- [x] All workflows auditable.
- [x] No technical implementation prematurely selected.
- [x] Saved to `docs/requirements/admin-workflows.md`.
