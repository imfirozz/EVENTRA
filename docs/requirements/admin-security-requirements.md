# EVENTRA — Admin Security Requirements

---

## 1. Overview & Purpose

This document defines the security requirements specific to EVENTRA's administrative functions. Admin accounts hold elevated privileges and require stronger security controls than standard user accounts. These requirements complement the platform-wide security baseline defined in `security-requirements-reference.md`.

---

## 2. Privileged Access Control

| Requirement ID    | Requirement                                                                                                                                          | Priority | Scope |
| :---------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------- | :------- | :---- |
| **ADMIN-SEC-001** | Admin accounts must be created exclusively by an existing `SUPER_ADMIN`. Self-registration is forbidden. Consistent with ADMIN-001.                  | Must     | MVP   |
| **ADMIN-SEC-002** | Admin dashboard routes and API endpoints must enforce `SUPER_ADMIN` or `READ_ONLY_ADMIN` role verification at every request. Consistent with FR-004. | Must     | MVP   |
| **ADMIN-SEC-003** | Non-admin roles (`ATTENDEE`, `ORGANIZER`, `GATE_STAFF`) must be denied access to all admin routes with 403 Forbidden.                                | Must     | MVP   |

---

## 3. RBAC Enforcement

| Requirement ID    | Requirement                                                                                                                           | Priority | Scope    |
| :---------------- | :------------------------------------------------------------------------------------------------------------------------------------ | :------- | :------- |
| **ADMIN-SEC-010** | All admin actions must be authorized against the actor's assigned admin role before execution.                                        | Must     | MVP      |
| **ADMIN-SEC-011** | A `READ_ONLY_ADMIN` must be denied any action beyond view/read operations. Consistent with admin-role-permission-matrix.md Section 5. | Should   | Advanced |
| **ADMIN-SEC-012** | Role checks must be performed server-side. Client-side role checks are insufficient and must not be the sole enforcement mechanism.   | Must     | MVP      |

---

## 4. Least Privilege

| Requirement ID    | Requirement                                                                                                                                          | Priority | Scope    |
| :---------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------- | :------- | :------- |
| **ADMIN-SEC-020** | Admin accounts must be assigned the minimum role necessary for their function. Default new admin creation should prompt for explicit role selection. | Must     | MVP      |
| **ADMIN-SEC-021** | The `READ_ONLY_ADMIN` role exists specifically to enforce least privilege for audit/compliance personnel who do not need modification capabilities.  | Should   | Advanced |

---

## 5. Separation of Duties

| Requirement ID    | Requirement                                                                                                                       | Priority                                                                     | Scope      |
| :---------------- | :-------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------- | :--------- |
| **ADMIN-SEC-030** | High-value refund approval (above a threshold) should require a second `SUPER_ADMIN` confirmation.                                | _OPEN BUSINESS DECISION_ — Threshold and approval process TBD (OBD-ADM-004). | Production |
| **ADMIN-SEC-031** | An admin must not be able to approve their own privilege escalation. Role changes must be performed by a different `SUPER_ADMIN`. | Should                                                                       | Advanced   |
| **ADMIN-SEC-032** | The last remaining active `SUPER_ADMIN` cannot deactivate or demote themselves. Consistent with ADMIN-005, ADMIN-160.             | Must                                                                         | MVP        |

---

## 6. Authentication Security

| Requirement ID    | Requirement                                                                                                                                                              | Priority                                                                           | Scope      |
| :---------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------- | :--------- |
| **ADMIN-SEC-040** | Admin accounts must use the same password hashing standard as all platform accounts (Argon2id or bcrypt, cost ≥ 12). Consistent with security-requirements-reference.md. | Must                                                                               | MVP        |
| **ADMIN-SEC-041** | Multi-factor authentication (MFA) for admin accounts.                                                                                                                    | _OPEN BUSINESS DECISION_ — Whether MFA is mandatory for admin login (OBD-ADM-002). | Production |
| **ADMIN-SEC-042** | Admin login attempts must be rate-limited. Consistent with the platform rate-limiting baseline (Auth: 5 req/min per security-requirements-reference.md).                 | Must                                                                               | MVP        |
| **ADMIN-SEC-043** | Failed admin login attempts must be logged with source information.                                                                                                      | Must                                                                               | MVP        |

---

## 7. Session Security

| Requirement ID    | Requirement                                                                                                                                                           | Priority | Scope    |
| :---------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------- | :------- |
| **ADMIN-SEC-050** | Admin sessions must use `HttpOnly`, `SameSite=Strict`, and `Secure` cookie flags. Consistent with security-requirements-reference.md.                                 | Must     | MVP      |
| **ADMIN-SEC-051** | Admin session timeout should be shorter than standard user sessions to reflect the elevated risk of privileged access. Exact duration is an _OPEN BUSINESS DECISION_. | Should   | Advanced |
| **ADMIN-SEC-052** | When an admin account is suspended or deactivated, all active sessions for that account must be immediately invalidated.                                              | Must     | MVP      |
| **ADMIN-SEC-053** | When an admin role is modified, all active sessions for that account must be invalidated to force re-authentication under the new role.                               | Should   | Advanced |
| **ADMIN-SEC-054** | A `SUPER_ADMIN` must be able to force-terminate all active sessions for a specific admin account (security incident response).                                        | Should   | Advanced |

---

## 8. Privilege Escalation Prevention

| Requirement ID    | Requirement                                                                                                                                                      | Priority | Scope    |
| :---------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------- | :------- |
| **ADMIN-SEC-060** | Non-admin users must not be able to access admin endpoints through URL manipulation, API calls, or parameter tampering.                                          | Must     | MVP      |
| **ADMIN-SEC-061** | A `READ_ONLY_ADMIN` must not be able to escalate to `SUPER_ADMIN` privileges through any mechanism other than explicit role assignment by another `SUPER_ADMIN`. | Should   | Advanced |
| **ADMIN-SEC-062** | Admin role assignment must be validated server-side. Client-submitted role values in requests must be ignored for authorization purposes.                        | Must     | MVP      |

---

## 9. Suspicious Activity Detection

| Requirement ID    | Requirement                                                                                                                               | Priority                                                                         | Scope      |
| :---------------- | :---------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------- | :--------- |
| **ADMIN-SEC-070** | Repeated failed admin login attempts (e.g., 5+ failures in 15 minutes) should trigger an alert or temporary lockout.                      | Should                                                                           | Advanced   |
| **ADMIN-SEC-071** | Admin actions performed outside of normal operating patterns (e.g., bulk cancellations, rapid-fire refunds) should be flagged for review. | Could                                                                            | Production |
| **ADMIN-SEC-072** | Admin access from unfamiliar locations or devices.                                                                                        | _OPEN BUSINESS DECISION_ — Whether location/device-based alerts are implemented. | Production |

---

## 10. Compromised Admin Account Response

| Requirement ID    | Requirement                                                                                                                                          | Priority                                                                | Scope      |
| :---------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------- | :--------- |
| **ADMIN-SEC-080** | A `SUPER_ADMIN` must be able to immediately suspend another admin account suspected of compromise.                                                   | Should                                                                  | Advanced   |
| **ADMIN-SEC-081** | Suspending an admin account must immediately invalidate all their active sessions (ADMIN-SEC-052).                                                   | Must                                                                    | MVP        |
| **ADMIN-SEC-082** | A compromised admin account must trigger review of all actions performed by that account during the suspected compromise window. Audit trail review. | Should                                                                  | Advanced   |
| **ADMIN-SEC-083** | Emergency admin recovery procedure (when the only SUPER_ADMIN is compromised).                                                                       | _OPEN BUSINESS DECISION_ — Emergency recovery workflow not yet defined. | Production |

---

## 11. High-Risk Action Controls

| Requirement ID    | Requirement                                                                                                                                                   | Priority | Scope    |
| :---------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------ | :------- | :------- |
| **ADMIN-SEC-090** | All destructive admin actions (cancellation, suspension, refund, account deactivation) must require explicit confirmation prompts. Consistent with ADMIN-191. | Must     | MVP      |
| **ADMIN-SEC-091** | Destructive admin actions must capture a reason/justification from the admin before execution.                                                                | Should   | Advanced |
| **ADMIN-SEC-092** | Bulk admin operations (e.g., cancelling all bookings for an event) must display impact summary (number affected, financial total) before confirmation.        | Should   | Advanced |

---

## 12. Emergency Controls

| Requirement ID    | Requirement                                                                                                                                                                      | Priority                                                       | Scope      |
| :---------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------- | :--------- |
| **ADMIN-SEC-100** | The platform must always have at least one active `SUPER_ADMIN` account. System must reject any action that would result in zero active SUPER_ADMINs. Consistent with ADMIN-005. | Must                                                           | MVP        |
| **ADMIN-SEC-101** | Emergency platform-wide session invalidation capability (force logout of all users).                                                                                             | Could                                                          | Production |
| **ADMIN-SEC-102** | Emergency event freeze capability (prevent all new bookings platform-wide).                                                                                                      | _OPEN BUSINESS DECISION_ — Whether emergency freeze is needed. | Production |

---

## 13. Security Incident Audit Requirements

| Requirement ID    | Requirement                                                                                                                                    | Priority | Scope      |
| :---------------- | :--------------------------------------------------------------------------------------------------------------------------------------------- | :------- | :--------- |
| **ADMIN-SEC-110** | All admin login attempts (successful and failed) must be logged with timestamp and source.                                                     | Must     | MVP        |
| **ADMIN-SEC-111** | All admin account status changes (creation, suspension, deactivation, role changes) must be logged.                                            | Must     | MVP        |
| **ADMIN-SEC-112** | All admin financial actions (refunds) must be logged with amount, reason, and booking reference.                                               | Should   | Advanced   |
| **ADMIN-SEC-113** | All admin moderation actions (event suspension, cancellation, organizer suspension) must be logged with reason.                                | Should   | Advanced   |
| **ADMIN-SEC-114** | Admin audit logs must be immutable (append-only). No admin, including SUPER_ADMIN, may modify or delete audit entries. Consistent with BR-013. | Should   | Production |

---

## 14. Definition of Done — Admin Security

- [x] Privileged access control requirements defined.
- [x] RBAC enforcement requirements defined.
- [x] Least privilege and separation of duties requirements defined.
- [x] Authentication and session security requirements defined.
- [x] Privilege escalation prevention requirements defined.
- [x] Suspicious activity detection requirements defined.
- [x] Compromised account response requirements defined.
- [x] High-risk action controls defined.
- [x] Emergency controls defined.
- [x] Security incident audit requirements defined.
- [x] All requirements consistent with security-requirements-reference.md and FR-004.
- [x] No technical implementation prematurely selected.
- [x] Saved to `docs/requirements/admin-security-requirements.md`.
