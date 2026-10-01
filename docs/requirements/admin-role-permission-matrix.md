# EVENTRA — Admin Role-Permission Matrix

---

## 1. Overview & Purpose

This document defines the complete role-permission matrix for EVENTRA administrative roles. It specifies exactly which actions each admin role may perform across all platform domains. The `READ_ONLY_ADMIN` role is strictly limited to view, audit, and reporting capabilities — it must not have any create, modify, delete, approve, financial, security, or configuration permissions.

---

## 2. Role Definitions

| Role                | Authority Level | Description                                                                                                                                                                              |
| :------------------ | :-------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **SUPER_ADMIN**     | Full            | Complete platform administration authority. All actions permitted.                                                                                                                       |
| **READ_ONLY_ADMIN** | View-Only       | Audit/compliance observer. Can view dashboards, reports, audit logs, and resource details. Cannot perform any modification, creation, deletion, approval, financial, or security action. |

> **Note**: Additional admin sub-roles (e.g., `FINANCE_ADMIN`, `SUPPORT_ADMIN`, `SECURITY_ADMIN`) are an _OPEN BUSINESS DECISION_ (OBD-ADM-001) for Production scope.

---

## 3. Permission Categories

| Category Code | Category        | Description                                                               |
| :------------ | :-------------- | :------------------------------------------------------------------------ |
| **V**         | View            | Read-only access to data, details, lists, and reports.                    |
| **C**         | Create          | Create new records (admin accounts, notifications).                       |
| **M**         | Modify          | Update, edit, or change status of existing records.                       |
| **D**         | Delete / Cancel | Remove, cancel, or invalidate records (bookings, tickets, events).        |
| **A**         | Approve         | Approve or authorize actions requiring elevated confirmation.             |
| **F**         | Financial       | Actions involving monetary transactions (refunds, payment investigation). |
| **S**         | Security        | Actions involving access control, incident response, account suspension.  |
| **CFG**       | Configuration   | Platform-level system configuration changes.                              |

---

## 4. Complete Role-Permission Matrix

### 4.1 Admin Account Management

| Action                     | SUPER_ADMIN | READ_ONLY_ADMIN | Notes                                          |
| :------------------------- | :---------- | :-------------- | :--------------------------------------------- |
| View admin account list    | ✅ V        | ✅ V            |                                                |
| View admin account details | ✅ V        | ✅ V            |                                                |
| Create admin account       | ✅ C        | ❌              | ADMIN-001                                      |
| Modify admin role          | ✅ M        | ❌              | ADMIN-006                                      |
| Deactivate admin account   | ✅ D        | ❌              | ADMIN-005 (cannot deactivate last SUPER_ADMIN) |
| Suspend admin account      | ✅ S        | ❌              |                                                |
| View admin audit trail     | ✅ V        | ✅ V            |                                                |

### 4.2 Customer (Attendee) Management

| Action                        | SUPER_ADMIN | READ_ONLY_ADMIN | Notes                                           |
| :---------------------------- | :---------- | :-------------- | :---------------------------------------------- |
| View customer list            | ✅ V        | ✅ V            | ADMIN-020                                       |
| Search customers              | ✅ V        | ✅ V            | ADMIN-021                                       |
| View customer account details | ✅ V        | ✅ V            | ADMIN-022                                       |
| View customer booking history | ✅ V        | ✅ V            | ADMIN-023, ADMIN-029                            |
| Suspend customer account      | ✅ S        | ❌              | ADMIN-024                                       |
| Reinstate customer account    | ✅ S        | ❌              | ADMIN-025                                       |
| View customer passwords       | ❌          | ❌              | ADMIN-026 — forbidden for ALL roles             |
| View raw payment card data    | ❌          | ❌              | ADMIN-027 / BRULE-004 — forbidden for ALL roles |

### 4.3 Organizer Management

| Action                            | SUPER_ADMIN | READ_ONLY_ADMIN | Notes                                |
| :-------------------------------- | :---------- | :-------------- | :----------------------------------- |
| View organizer list               | ✅ V        | ✅ V            | ADMIN-030                            |
| View organizer account details    | ✅ V        | ✅ V            | ADMIN-031                            |
| Suspend organizer account         | ✅ S        | ❌              | ADMIN-032                            |
| Reinstate organizer account       | ✅ S        | ❌              | ADMIN-033                            |
| Review organizer verification     | ✅ A        | ❌              | ADMIN-035 (_OPEN BUSINESS DECISION_) |
| View organizer moderation history | ✅ V        | ✅ V            |                                      |

### 4.4 Event Moderation & Governance

| Action                                 | SUPER_ADMIN | READ_ONLY_ADMIN | Notes     |
| :------------------------------------- | :---------- | :-------------- | :-------- |
| View all events (platform-wide)        | ✅ V        | ✅ V            | ADMIN-040 |
| View event details, tickets, inventory | ✅ V        | ✅ V            | ADMIN-047 |
| Suspend event                          | ✅ M        | ❌              | ADMIN-041 |
| Reinstate suspended event              | ✅ M        | ❌              | ADMIN-043 |
| Administratively cancel event          | ✅ D        | ❌              | ADMIN-044 |
| Flag event for review                  | ✅ M        | ❌              | ADMIN-100 |
| View event moderation history          | ✅ V        | ✅ V            | ADMIN-048 |

### 4.5 Venue & Inventory Administration

| Action                             | SUPER_ADMIN              | READ_ONLY_ADMIN | Notes                   |
| :--------------------------------- | :----------------------- | :-------------- | :---------------------- |
| View venue/inventory configuration | ✅ V                     | ✅ V            | ADMIN-050               |
| Modify venue/inventory settings    | _OPEN BUSINESS DECISION_ | ❌              | ADMIN-051 (OBD-ADM-003) |

### 4.6 Booking & Order Administration

| Action                            | SUPER_ADMIN | READ_ONLY_ADMIN | Notes     |
| :-------------------------------- | :---------- | :-------------- | :-------- |
| Search bookings                   | ✅ V        | ✅ V            | ADMIN-060 |
| View booking details              | ✅ V        | ✅ V            | ADMIN-061 |
| View booking state history        | ✅ V        | ✅ V            | ADMIN-062 |
| Cancel booking (admin)            | ✅ D        | ❌              | ADMIN-063 |
| View booking cancellation history | ✅ V        | ✅ V            | ADMIN-066 |

### 4.7 Payment & Refund Administration

| Action                            | SUPER_ADMIN | READ_ONLY_ADMIN | Notes                                                 |
| :-------------------------------- | :---------- | :-------------- | :---------------------------------------------------- |
| View payment transaction details  | ✅ V        | ✅ V            | ADMIN-070                                             |
| View raw credit card data         | ❌          | ❌              | ADMIN-071 / BRULE-004 — forbidden for ALL roles       |
| Initiate refund                   | ✅ F        | ❌              | ADMIN-072                                             |
| Approve high-value refund         | ✅ A        | ❌              | ADMIN-074 (_OPEN BUSINESS DECISION_)                  |
| View refund history               | ✅ V        | ✅ V            | ADMIN-076                                             |
| Investigate payment discrepancies | ✅ V        | ✅ V            | ADMIN-075 (view); resolution action: SUPER_ADMIN only |

### 4.8 Ticket Administration

| Action                           | SUPER_ADMIN | READ_ONLY_ADMIN | Notes     |
| :------------------------------- | :---------- | :-------------- | :-------- |
| View ticket details              | ✅ V        | ✅ V            | ADMIN-080 |
| Invalidate ticket                | ✅ D        | ❌              | ADMIN-081 |
| View ticket invalidation history | ✅ V        | ✅ V            | ADMIN-083 |

### 4.9 Event Staff Administration

| Action                              | SUPER_ADMIN | READ_ONLY_ADMIN | Notes     |
| :---------------------------------- | :---------- | :-------------- | :-------- |
| View staff assignments (all events) | ✅ V        | ✅ V            | ADMIN-090 |
| Remove staff assignment             | ✅ M        | ❌              | ADMIN-091 |
| View staff change history           | ✅ V        | ✅ V            | ADMIN-093 |

### 4.10 Support & Customer Service

| Action                      | SUPER_ADMIN | READ_ONLY_ADMIN | Notes     |
| :-------------------------- | :---------- | :-------------- | :-------- |
| Lookup booking for support  | ✅ V        | ✅ V            | ADMIN-110 |
| Verify ticket validity      | ✅ V        | ✅ V            | ADMIN-111 |
| Process support refund      | ✅ F        | ❌              | ADMIN-112 |
| View support action history | ✅ V        | ✅ V            | ADMIN-114 |

### 4.11 Reporting & Analytics

| Action                     | SUPER_ADMIN | READ_ONLY_ADMIN | Notes                                |
| :------------------------- | :---------- | :-------------- | :----------------------------------- |
| View platform-wide reports | ✅ V        | ✅ V            | ADMIN-120 through ADMIN-123          |
| View event-level reports   | ✅ V        | ✅ V            | ADMIN-121                            |
| View financial reports     | ✅ V        | ✅ V            | ADMIN-122                            |
| Export reports             | ✅ V        | ✅ V            | ADMIN-124 (_OPEN BUSINESS DECISION_) |

### 4.12 System Configuration

| Action                        | SUPER_ADMIN | READ_ONLY_ADMIN | Notes     |
| :---------------------------- | :---------- | :-------------- | :-------- |
| View configuration settings   | ✅ V        | ✅ V            | ADMIN-132 |
| Modify configuration settings | ✅ CFG      | ❌              | ADMIN-130 |

### 4.13 Notification Administration

| Action                         | SUPER_ADMIN | READ_ONLY_ADMIN | Notes                |
| :----------------------------- | :---------- | :-------------- | :------------------- |
| Trigger platform notifications | ✅ C        | ❌              | ADMIN-140            |
| View notification status/logs  | ✅ V        | ✅ V            | ADMIN-141, ADMIN-142 |

### 4.14 Audit Trail

| Action                      | SUPER_ADMIN | READ_ONLY_ADMIN | Notes                                    |
| :-------------------------- | :---------- | :-------------- | :--------------------------------------- |
| View audit trail            | ✅ V        | ✅ V            | Both roles may review audit records.     |
| Export audit trail          | ✅ V        | ✅ V            | _OPEN BUSINESS DECISION_ — export scope. |
| Modify/delete audit entries | ❌          | ❌              | Forbidden for ALL roles — append-only.   |

---

## 5. READ_ONLY_ADMIN — Explicit Boundary Statement

The `READ_ONLY_ADMIN` role is strictly constrained to:

**PERMITTED (View/Audit only)**:

- View admin account list and details
- View customer accounts, organizer accounts, events, bookings, payments, tickets, staff assignments
- View booking state history and moderation history
- View all reports and analytics
- View audit trail records
- View system configuration (read-only)
- View notification delivery status

**EXPLICITLY FORBIDDEN**:

- ❌ Create any record (admin accounts, notifications)
- ❌ Modify any record (status changes, suspensions, reinstatements)
- ❌ Delete or cancel any record (bookings, tickets, events)
- ❌ Approve any action (refunds, verifications)
- ❌ Perform any financial action (refunds, payment operations)
- ❌ Perform any security action (account suspension, incident response)
- ❌ Modify any configuration setting
- ❌ Trigger any notification
- ❌ Modify or delete audit trail entries

---

## 6. Universally Forbidden Actions (All Admin Roles)

| Action                                | Forbidden For | Rationale                                                             |
| :------------------------------------ | :------------ | :-------------------------------------------------------------------- |
| View/access user passwords (any role) | ALL           | Passwords are hashed; never viewable. ADMIN-026, ADMIN-180.           |
| View/access raw credit card data      | ALL           | BRULE-004: Zero local card storage. ADMIN-027, ADMIN-071, ADMIN-181.  |
| Modify/delete audit trail entries     | ALL           | Audit trail is append-only and tamper-resistant. BR-013.              |
| Self-deactivate last SUPER_ADMIN      | ALL           | ADMIN-005: Platform must always have at least one active SUPER_ADMIN. |

---

## 7. Open Permission Decisions

| Decision ID      | Area                       | Description                                                            | Impact                                  |
| :--------------- | :------------------------- | :--------------------------------------------------------------------- | :-------------------------------------- |
| **OBD-PERM-001** | Admin Venue Override       | Whether `SUPER_ADMIN` can directly modify organizer venue/inventory.   | Affects ADMIN-051.                      |
| **OBD-PERM-002** | High-Value Refund Approval | Whether refunds above a threshold require second-admin approval.       | Affects ADMIN-074 separation of duties. |
| **OBD-PERM-003** | Additional Admin Roles     | Whether `FINANCE_ADMIN`, `SUPPORT_ADMIN`, `SECURITY_ADMIN` are needed. | Affects entire permission matrix.       |
| **OBD-PERM-004** | Report Export Scope        | What data `READ_ONLY_ADMIN` may export in reports.                     | Affects ADMIN-124 and privacy.          |

---

## 8. Definition of Done — Role-Permission Matrix

- [x] SUPER_ADMIN permissions fully defined across all domains.
- [x] READ_ONLY_ADMIN explicitly limited to view/audit/reporting only.
- [x] READ_ONLY_ADMIN forbidden from all create/modify/delete/approve/financial/security/config actions.
- [x] Universally forbidden actions defined (passwords, card data, audit modification, last-admin self-deactivation).
- [x] Permission categories (V/C/M/D/A/F/S/CFG) clearly distinguished.
- [x] Open permission decisions catalogued.
- [x] No technical authorization mechanism prematurely selected.
- [x] Saved to `docs/requirements/admin-role-permission-matrix.md`.
