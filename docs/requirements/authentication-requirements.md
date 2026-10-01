# EVENTRA — Authentication & Account Security Requirements

---

## 1. Overview & Scope

This document specifies the authoritative **Authentication & Account Security Requirements (REQ-AUTH-001 to REQ-AUTH-010)** for EVENTRA. It defines system behavior for user identity verification, credential management, session lifecycles, account recovery, rate limiting, and step-up security.

These requirements focus on functional security behavior and policy rules while leaving specific underlying implementations (such as session storage drivers, cryptography libraries, database schemas, or API protocol frameworks) to subsequent architectural design phases.

---

## 2. Authentication Requirements Specification

---

### REQ-AUTH-001: User Registration & Account Creation

- **Requirement ID**: `REQ-AUTH-001`
- **Requirement Name**: User Registration & Account Creation
- **Requirement Statement**: THE SYSTEM SHALL enable unauthenticated users to create an account by providing an email address, password, and full name, verifying email uniqueness, validating credential strength, and issuing an initial unverified or active user identity.
- **Actor / Initiator**: Unauthenticated User (Attendee or Organizer)
- **Inputs & Parameters**:
  - Full Name (String, 2-100 characters)
  - Email Address (String, valid RFC 5322 syntax)
  - Password (String, minimum 8 characters, requiring at least 1 uppercase letter, 1 lowercase letter, 1 number, and 1 special character)
  - Requested Account Type (`ATTENDEE` or `ORGANIZER`)
- **Processing Rules & Business Constraints**:
  1. THE SYSTEM SHALL verify that the provided email address does not already exist in the user repository (case-insensitive check).
  2. THE SYSTEM SHALL enforce password complexity rules prior to accepting credentials.
  3. THE SYSTEM SHALL hash the password using a cryptographically secure key-derivation function before persistence.
  4. THE SYSTEM SHALL assign the requested role (`ATTENDEE` by default; `ORGANIZER` subject to verification policy).
  5. THE SYSTEM SHALL initialize the account state to `UNVERIFIED` if email verification is enabled, or `ACTIVE` if instant access is configured.
  6. THE SYSTEM SHALL dispatch an email verification notification containing a secure, single-use, time-bound verification token (TTL: 24 hours).
- **Outputs & State Changes**:
  - New user identity created with state (`UNVERIFIED` / `ACTIVE`).
  - Account creation event logged in security audit logs.
  - Active session token generated (if instant post-registration login is active).
- **Security & Policy Controls**:
  - Rate limit registration attempts per IP address (max 5 registrations per hour per IP).
  - Sanitization of user input strings to prevent script injection.
- **Error Handling & Edge Cases**:
  - *Duplicate Email*: System shall return a generic error message or notification indicating the email is unavailable without disclosing sensitive account metadata.
  - *Weak Password*: System shall return specific validation errors indicating which criteria failed.
- **Priority**: Must | **Scope**: MVP
- **Traceability**: `BR-009`, `PR-001`, `FR-001`, `US-001`, `UC-001`, `AC-001`

---

### REQ-AUTH-002: Credential-Based Authentication & Password Verification

- **Requirement ID**: `REQ-AUTH-002`
- **Requirement Name**: Credential-Based Authentication & Password Verification
- **Requirement Statement**: THE SYSTEM SHALL authenticate users presenting valid email and password credentials, verify account status, generate an authenticated session upon success, and enforce security policies against failed attempts.
- **Actor / Initiator**: Registered User
- **Inputs & Parameters**:
  - Email Address (String)
  - Password (String)
  - Remember Me Flag (Boolean, optional)
- **Processing Rules & Business Constraints**:
  1. THE SYSTEM SHALL normalize the email input (lowercase, trim) and locate the associated user identity.
  2. THE SYSTEM SHALL verify whether the account state is `ACTIVE`. If the account is `SUSPENDED` or `DISABLED`, login shall be denied.
  3. THE SYSTEM SHALL compare the presented password against the stored password hash using a constant-time comparison algorithm to prevent timing attacks.
  4. Upon successful validation:
     - THE SYSTEM SHALL reset failed login counter to zero.
     - THE SYSTEM SHALL issue a new authenticated session token with encoded user identity, assigned roles, and granted permissions.
     - THE SYSTEM SHALL set session TTL based on policy (Default: 2 hours of inactivity; Extended: 30 days if "Remember Me" selected).
- **Outputs & State Changes**:
  - Authenticated session established; secure authentication token issued.
  - User `last_login_at` timestamp updated.
  - Audit record created for successful authentication.
- **Security & Policy Controls**:
  - Generic error response on failure ("Invalid email or password") to prevent username enumeration.
  - Constant-time verification loop.
- **Error Handling & Edge Cases**:
  - *Invalid Credentials*: Increment failed attempt counter; return 401 Unauthorized.
  - *Account Suspended*: Return 403 Forbidden with "Account suspended" explanation message.
  - *Unverified Email (if enforced)*: Prompt user to complete email verification while restricting access to protected features.
- **Priority**: Must | **Scope**: MVP
- **Traceability**: `BR-009`, `BR-016`, `PR-001`, `PR-027`, `FR-002`, `US-002`, `UC-002`, `AC-002`

---

### REQ-AUTH-003: Session Lifecycle & Invalidation Management

- **Requirement ID**: `REQ-AUTH-003`
- **Requirement Name**: Session Lifecycle & Invalidation Management
- **Requirement Statement**: THE SYSTEM SHALL manage user session states, automatically expire inactive sessions, enforce explicit user logout by invalidating active tokens, and provide global session revocation capabilities.
- **Actor / Initiator**: Authenticated User / System Daemon / Security Admin
- **Inputs & Parameters**:
  - Session Identifier / Token
  - Revocation Scope (`CURRENT_SESSION`, `ALL_OTHER_SESSIONS`, `ALL_SESSIONS`)
- **Processing Rules & Business Constraints**:
  1. THE SYSTEM SHALL validate session tokens on every protected request for validity, signature integrity, and non-expiration.
  2. THE SYSTEM SHALL automatically invalidate sessions exceeding the maximum absolute lifetime (24 hours for standard, 30 days for persistent) or idle timeout (30 minutes of inactivity).
  3. WHEN a user explicitly requests logout (`CURRENT_SESSION`), THE SYSTEM SHALL mark the session token invalid server-side and instruct the client to clear credentials.
  4. WHEN a user selects "Log out of all devices" (`ALL_SESSIONS`), THE SYSTEM SHALL invalidate all session tokens issued to that user ID.
  5. WHEN a user changes their password or updates security credentials, THE SYSTEM SHALL automatically revoke all other existing active sessions.
- **Outputs & State Changes**:
  - Session state transitioned from `ACTIVE` to `EXPIRED` or `REVOKED`.
  - Client state cleared.
- **Security & Policy Controls**:
  - Server-side token blacklisting/revocation check.
  - Secure transport enforcement (HTTPS/TLS only).
- **Error Handling & Edge Cases**:
  - *Expired / Revoked Token Access*: Return 401 Unauthorized; redirect to login.
  - *Malformed Token*: Reject immediately, record security event.
- **Priority**: Must | **Scope**: MVP
- **Traceability**: `BR-009`, `BR-016`, `PR-001`, `FR-003`, `US-003`, `UC-003`, `AC-003`

---

### REQ-AUTH-004: Multi-Factor Authentication (MFA / 2FA)

- **Requirement ID**: `REQ-AUTH-004`
- **Requirement Name**: Multi-Factor Authentication (MFA / 2FA)
- **Requirement Statement**: THE SYSTEM SHALL support secondary authentication factor verification (Time-based One-Time Password - TOTP) for users who opt into MFA or roles requiring mandatory step-up security (Organizers, Admins).
- **Actor / Initiator**: Authenticated User / System Security Manager
- **Inputs & Parameters**:
  - Account ID
  - TOTP Code (6-digit numeric string) / Backup Recovery Code
- **Processing Rules & Business Constraints**:
  1. WHEN MFA is enabled on an account, primary credential validation (Email + Password) SHALL yield a temporary pre-authentication session token (TTL: 5 minutes) restricted solely to the MFA verification endpoint.
  2. THE SYSTEM SHALL verify the presented 6-digit TOTP code against the shared secret using a time window of $\pm 1$ interval (30-second duration).
  3. THE SYSTEM SHALL provide 8 single-use numeric recovery codes upon MFA setup.
  4. WHEN a recovery code is presented, THE SYSTEM SHALL verify its validity, consume it (render single-use), and grant authenticated access.
  5. Organizers handling financial payouts and Admins SHALL be required to enable MFA prior to accessing privileged administrative tools.
- **Outputs & State Changes**:
  - Pre-auth token upgraded to full role-gated session token.
  - Recovery code marked as used.
- **Security & Policy Controls**:
  - Max 3 consecutive failed TOTP attempts per pre-auth session before invalidating pre-auth token.
- **Error Handling & Edge Cases**:
  - *Invalid TOTP Code*: Return "Invalid verification code", retain pre-auth state.
  - *Expired Pre-Auth Token*: Require user to re-enter primary credentials.
- **Priority**: Should | **Scope**: Advanced Tier (Optional for Attendee MVP; Required for Admin Advanced)
- **Traceability**: `BR-016`, `PR-027`, `FR-002`, `NFR-SEC-004`

---

### REQ-AUTH-005: Federated Social Identity (OAuth 2.0 / OIDC)

- **Requirement ID**: `REQ-AUTH-005`
- **Requirement Name**: Federated Social Identity (OAuth 2.0 / OIDC)
- **Requirement Statement**: THE SYSTEM SHALL allow users to register and authenticate using trusted third-party identity providers (Google, Apple) via OpenID Connect (OIDC) protocol flows.
- **Actor / Initiator**: Unauthenticated / Authenticated User
- **Inputs & Parameters**:
  - Identity Provider Name (`Google`, `Apple`)
  - Authorization Grant Code & State Parameter
- **Processing Rules & Business Constraints**:
  1. THE SYSTEM SHALL execute authorization code exchange with Proof Key for Code Exchange (PKCE) with the federated provider.
  2. THE SYSTEM SHALL extract verified user claims (subject ID, email, name, email verification status).
  3. IF no account exists matching the provider subject ID or email address, THE SYSTEM SHALL automatically provision a new `ATTENDEE` account with state `ACTIVE`.
  4. IF an account exists with matching verified email address, THE SYSTEM SHALL link the social provider subject ID to the existing user identity.
  5. THE SYSTEM SHALL issue a standard EVENTRA session token upon successful federated validation.
- **Outputs & State Changes**:
  - Social identity link recorded.
  - Authenticated session established.
- **Security & Policy Controls**:
  - Strict state parameter verification to prevent Cross-Site Request Forgery (CSRF).
  - Nonce validation for OIDC ID tokens.
- **Error Handling & Edge Cases**:
  - *Unverified Social Email*: Reject automatic linkage; require manual email confirmation.
  - *Provider Authorization Denied*: Redirect to login view with explanatory message.
- **Priority**: Could | **Scope**: Advanced Tier
- **Traceability**: `BR-009`, `PR-001`, `FR-001`, `FR-002`

---

### REQ-AUTH-006: Password Reset & Account Recovery

- **Requirement ID**: `REQ-AUTH-006`
- **Requirement Name**: Password Reset & Account Recovery
- **Requirement Statement**: THE SYSTEM SHALL provide a self-service password reset workflow allowing users to regain access to their accounts via cryptographically secure, time-sensitive reset links sent to their registered email address.
- **Actor / Initiator**: Unauthenticated User
- **Inputs & Parameters**:
  - Account Email Address
  - Reset Token (URL Parameter)
  - New Password & Confirmation Password
- **Processing Rules & Business Constraints**:
  1. WHEN a password reset request is submitted, THE SYSTEM SHALL generate a cryptographically random, high-entropy reset token with a 15-minute expiration window (TTL: 900 seconds).
  2. THE SYSTEM SHALL send an email containing the reset URL with token to the specified address.
  3. IF the email does not exist in the database, THE SYSTEM SHALL return a success confirmation UI message without dispatching an email, preventing account enumeration.
  4. WHEN the user accesses the reset URL with a valid, non-expired, unconsumed token, THE SYSTEM SHALL display the new password prompt.
  5. Upon receiving a valid new password meeting complexity requirements:
     - THE SYSTEM SHALL update the stored password hash.
     - THE SYSTEM SHALL mark the reset token as consumed.
     - THE SYSTEM SHALL invalidate all active session tokens for the account (`REQ-AUTH-003`).
     - THE SYSTEM SHALL send a password change notification email.
- **Outputs & State Changes**:
  - Password hash updated; existing sessions revoked.
  - Reset token invalidated.
- **Security & Policy Controls**:
  - Single-use reset tokens.
  - Rate limit reset requests (max 3 requests per hour per email).
- **Error Handling & Edge Cases**:
  - *Expired / Reused Token*: Display error "Reset link invalid or expired"; offer new request link.
  - *Password Re-use*: Optionally restrict changing password to the exact current password.
- **Priority**: Must | **Scope**: MVP
- **Traceability**: `BR-009`, `BR-016`, `PR-001`, `FR-001`, `US-001`, `UC-001`

---

### REQ-AUTH-007: Account Lockout & Brute-Force Rate Limiting

- **Requirement ID**: `REQ-AUTH-007`
- **Requirement Name**: Account Lockout & Brute-Force Rate Limiting
- **Requirement Statement**: THE SYSTEM SHALL monitor authentication failures, temporarily lock accounts experiencing excessive failed login attempts, and enforce IP-level rate limiting to defend against credential stuffing and brute-force attacks.
- **Actor / Initiator**: System Security Enforcer / Malicious Actor
- **Inputs & Parameters**:
  - Target Email Address / Account ID
  - Source IP Address
- **Processing Rules & Business Constraints**:
  1. THE SYSTEM SHALL maintain a count of consecutive failed authentication attempts per user email.
  2. WHEN failed login attempts reach **5 consecutive failures** within a 15-minute window:
     - THE SYSTEM SHALL temporarily lock the account for a duration of **15 minutes**.
     - THE SYSTEM SHALL log a security warning event (`BRUTE_FORCE_LOCKOUT`).
     - THE SYSTEM SHALL dispatch a security alert email to the account owner.
  3. Subsequent authentication attempts during the lockout window SHALL fail immediately with "Account temporarily locked for security. Try again later or reset password."
  4. Successful authentication prior to reaching the threshold SHALL reset the failure counter to zero.
  5. THE SYSTEM SHALL restrict login endpoint access per IP address to maximum 20 requests per minute.
- **Outputs & State Changes**:
  - Account state temporarily set to `LOCKED` (until lockout expiration).
  - IP address rate-limited at API edge.
- **Security & Policy Controls**:
  - Exponential backoff after repeated lockouts.
  - Admin override capability to unlock legitimate user accounts.
- **Error Handling & Edge Cases**:
  - *Attempt During Lockout*: Return 429 Too Many Requests / 403 Forbidden with retry-after header.
- **Priority**: Must | **Scope**: MVP
- **Traceability**: `BR-016`, `PR-027`, `FR-002`, `NFR-SEC-002`

---

### REQ-AUTH-008: Role-Based Access Control (RBAC) Enforcement

- **Requirement ID**: `REQ-AUTH-008`
- **Requirement Name**: Role-Based Access Control (RBAC) Enforcement
- **Requirement Statement**: THE SYSTEM SHALL enforce strict Role-Based Access Control on every API request and interface action, ensuring actors execute only operations explicitly authorized for their assigned primary and secondary roles.
- **Actor / Initiator**: Authenticated User (Attendee, Organizer, Gate Staff, Super Admin)
- **Inputs & Parameters**:
  - Authenticated Session Context (User ID, Assigned Roles)
  - Requested Resource & Permission Action (`event:create`, `ticket:scan`, `payout:approve`)
- **Processing Rules & Business Constraints**:
  1. System roles SHALL be defined as:
     - `ATTENDEE`: Standard customer registered to discover events, hold inventory, purchase tickets, and view personal bookings.
     - `ORGANIZER`: Event creator authorized to design events, configure ticket tiers, view event analytics, and manage payouts.
     - `GATE_STAFF`: Operational role authorized strictly to scan and validate ticket QR codes for assigned events.
     - `SUPER_ADMIN`: System administrator authorized for platform governance, refund overrides, user suspension, and global audit inspection.
  2. THE SYSTEM SHALL verify that the authenticated actor possesses the exact role required by the target resource endpoint guard.
  3. Access attempts by actors missing required roles SHALL be blocked prior to executing business logic.
- **Outputs & State Changes**:
  - Operation authorized and executed, or access denied.
  - Authorization violation logged for forbidden attempts.
- **Security & Policy Controls**:
  - Default closed policy: all protected routes require explicit role authorization.
- **Error Handling & Edge Cases**:
  - *Missing Role*: Return 403 Forbidden with "Insufficient permissions".
  - *Unauthenticated Access*: Return 401 Unauthorized.
- **Priority**: Must | **Scope**: MVP
- **Traceability**: `BR-009`, `BR-016`, `PR-027`, `FR-004`, `US-004`, `UC-004`, `AC-004`

---

### REQ-AUTH-009: Resource Ownership & Ownership Guarding

- **Requirement ID**: `REQ-AUTH-009`
- **Requirement Name**: Resource Ownership & Ownership Guarding
- **Requirement Statement**: THE SYSTEM SHALL complement Role-Based Access Control with fine-grained Resource Ownership checks, ensuring that non-admin actors can access, modify, or cancel only entities (bookings, tickets, event configurations) explicitly owned by or delegated to them.
- **Actor / Initiator**: Authenticated User
- **Inputs & Parameters**:
  - User Identity Context (User ID)
  - Target Entity ID (e.g., Booking ID, Event ID, Ticket ID)
- **Processing Rules & Business Constraints**:
  1. WHEN an `ATTENDEE` attempts to view, download, or request a refund for a booking, THE SYSTEM SHALL verify `Booking.customer_id == Session.user_id`.
  2. WHEN an `ORGANIZER` attempts to edit, publish, or cancel an event or view payouts, THE SYSTEM SHALL verify `Event.organizer_id == Session.user_id`.
  3. WHEN a `GATE_STAFF` user attempts to scan tickets, THE SYSTEM SHALL verify `StaffAssignment.event_id == Ticket.event_id` and `StaffAssignment.user_id == Session.user_id`.
  4. `SUPER_ADMIN` actors SHALL bypass ownership checks subject to strict administrative audit logging.
  5. IF ownership evaluation fails, THE SYSTEM SHALL deny access regardless of whether the user possesses the correct generic role.
- **Outputs & State Changes**:
  - Resource access granted or denied.
  - Ownership violation logged as security audit event (`IDOR_ATTEMPT_PREVENTED`).
- **Security & Policy Controls**:
  - Defence against Insecure Direct Object Reference (IDOR) vulnerabilities.
- **Error Handling & Edge Cases**:
  - *Ownership Mismatch*: Return 404 Not Found or 403 Forbidden to prevent entity existence probing.
- **Priority**: Must | **Scope**: MVP
- **Traceability**: `BR-016`, `PR-027`, `FR-004`, `US-004`, `UC-004`, `AC-004`

---

### REQ-AUTH-010: Security Audit Logging & Authentication Event Tracking

- **Requirement ID**: `REQ-AUTH-010`
- **Requirement Name**: Security Audit Logging & Authentication Event Tracking
- **Requirement Statement**: THE SYSTEM SHALL record immutable security audit logs for all identity lifecycle, authentication, session state, authorization, and privilege escalation events for security auditing and compliance.
- **Actor / Initiator**: System Security Subsystem
- **Inputs & Parameters**:
  - Event Type (`LOGIN_SUCCESS`, `LOGIN_FAILED`, `LOGOUT`, `PASSWORD_RESET`, `ROLE_CHANGE`, `ACCOUNT_LOCKED`, `UNAUTHORIZED_ACCESS_ATTEMPT`)
  - User ID (if identifiable)
  - IP Address & User Agent String
  - Timestamp (UTC ISO-8601)
  - Resource URI & Status Code
- **Processing Rules & Business Constraints**:
  1. THE SYSTEM SHALL write audit log entries synchronously or via reliable asynchronous queuing for every security-relevant event.
  2. Log entries SHALL contain no sensitive plain-text credentials, raw passwords, secret keys, or full payment card numbers.
  3. Audit logs SHALL be append-only and protected against modification or truncation by any user, including `SUPER_ADMIN`.
  4. Audit log records SHALL be retained for a minimum of 365 days for regulatory compliance and threat investigation.
- **Outputs & State Changes**:
  - Permanent audit record appended to security log repository.
- **Security & Policy Controls**:
  - Log tampering protection.
  - Sensitive data masking/redaction filters.
- **Error Handling & Edge Cases**:
  - *Audit Store Unavailable*: Fall back to local secure log queue; alert system administrators.
- **Priority**: Must | **Scope**: MVP
- **Traceability**: `BR-016`, `PR-027`, `NFR-SEC-005`

---

## 3. Summary of Authentication Requirements Matrix

| REQ ID | Requirement Name | Actor | Priority | Scope | Primary Control |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `REQ-AUTH-001` | User Registration & Account Creation | Unauthenticated | Must | MVP | Email Uniqueness & Password Strength |
| `REQ-AUTH-002` | Credential Authentication & Verification | Registered User | Must | MVP | Constant-Time Hash Check & Session Issue |
| `REQ-AUTH-003` | Session Lifecycle & Invalidation | Authenticated User | Must | MVP | Server-Side Revocation & Inactivity Timeout |
| `REQ-AUTH-004` | Multi-Factor Authentication (MFA) | Privileged / Opt-in | Should | Advanced | TOTP Step-Up & Recovery Codes |
| `REQ-AUTH-005` | Federated Social Identity (OAuth/OIDC) | Social User | Could | Advanced | PKCE OAuth Exchange & Account Linkage |
| `REQ-AUTH-006` | Password Reset & Account Recovery | Account Owner | Must | MVP | Time-bound High-Entropy Reset Tokens |
| `REQ-AUTH-007` | Account Lockout & Brute-Force Defense | System / Attacker | Must | MVP | 5 Failures / 15-min Lockout & IP Limits |
| `REQ-AUTH-008` | Role-Based Access Control (RBAC) | All Users | Must | MVP | Server-Side Role Guard Interception |
| `REQ-AUTH-009` | Resource Ownership & IDOR Protection | All Users | Must | MVP | Entity Owner Matching (`user_id == owner_id`) |
| `REQ-AUTH-010` | Security Audit Logging & Tracking | System Subsystem | Must | MVP | Append-Only Masked Security Trail |

---
