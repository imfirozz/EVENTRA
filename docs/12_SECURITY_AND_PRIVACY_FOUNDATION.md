# EVENTRA — Security & Privacy Foundation

---

## 1. Foundational Security Principles

> [!IMPORTANT] Core Security Architecture
> EVENTRA enforces a **Defensive by Default** posture across all application layers. Security and privacy controls are designed into the modular monolith from day one, rather than retrofitted.

---

### A. Authentication & Session Management
- **Strong Password Hashing**: Passwords stored using Argon2id (or bcrypt with cost factor $\ge 12$). Zero plain-text password storage.
- **Session Security**: HTTP-only, `SameSite=Strict`, `Secure` flags enforced on all session cookies / JWT tokens.
- **Token Invalidation**: Server-side session/token revocation upon logout or password change.

### B. Authorization & Least Privilege
- **Role-Based Access Control (RBAC)**: Strict role separation (`ATTENDEE`, `ORGANIZER`, `GATE_STAFF`, `SUPER_ADMIN`).
- **Principle of Least Privilege**: Database user accounts possess only necessary CRUD permissions; application containers run under non-root Docker users.
- **Resource Ownership Verification**: API endpoints explicitly verify that the requesting user owns the target resource (e.g. an organizer can only edit their own events).

### C. Input Validation & Sanitization
- **Strict Schema Validation**: 100% of incoming HTTP payloads validated against Zod schemas before hitting business logic.
- **Injection Prevention**: Parameterized SQL queries via ORM/Query Builder (Knex/Prisma/PG) to eliminate SQL injection.
- **XSS Prevention**: Strict HTML escaping on public catalog components; Content Security Policy (CSP) headers enforced.

### D. Secrets Management & Encryption
- **Zero Hardcoded Secrets**: Secret keys, DB passwords, and API credentials injected exclusively via environment variables (`.env`).
- **Encryption in Transit**: TLS 1.3 enforced for all external HTTP traffic and internal service communication.
- **Encryption at Rest**: Sensitive DB fields encrypted; digital QR codes HMAC-SHA256 signed with server-side secrets.

### E. Sensitive Data Minimization & Privacy
- **Zero Card Data Storage**: 100% PCI-DSS SAQ A compliance delegation to Stripe. Zero credit card numbers touch EVENTRA servers.
- **PII Minimization**: Only collect essential attendee fields (Name, Email).
- **Right to Erasure**: Anonymize attendee data upon request after event completion.

### F. Secure Logging & Auditability
- **PII Scrubbing**: Logger explicitly redacts passwords, tokens, emails, and financial payloads before writing logs.
- **Immutable Audit Logging**: Administrative mutations, role changes, and ticket status transitions appended to an immutable audit trail.

### G. Rate Limiting & Denial of Service Protection
- **API Rate Limiting**: Redis-backed sliding window rate limiting on sensitive routes (`/api/auth/login` max 5 req/min, `/api/bookings/reserve` max 10 req/min).

### H. Dependency & Infrastructure Security
- **Automated Dependency Scanning**: Run `npm audit` and Snyk in CI pipeline to block vulnerable dependencies.
- **Network Isolation**: PostgreSQL and Redis containers isolated on an internal Docker bridge network; not exposed to public ports.
- **Encrypted Backups**: Database backup snapshots encrypted with AES-256 before offsite storage.

---

## 2. High-Level Threat Baseline (STRIDE Alignment)

### A. System Assets
1. User Credentials & Password Hashes
2. Ticket Inventory Allocation State
3. HMAC Cryptographic Signing Keys
4. Payment Webhook Verification Secrets
5. Attendee PII (Name, Email)
6. System Audit Logs

### B. System Actors
1. **Anonymous Visitor**: Public catalog browser.
2. **Authenticated Attendee**: Ticket buyer.
3. **Event Organizer**: Event creator & ticket manager.
4. **Gate Staff**: Mobile QR ticket scanner operator.
5. **Super Admin**: System moderator.
6. **External Malicious Actor**: Unauthenticated attacker attempting API abuse or fraud.

### C. Trust Boundaries

```
[ External User / Browser ]
          │  (Trust Boundary 1: Public Internet / TLS 1.3)
          ▼
[ Nginx Reverse Proxy / Web Server ]
          │  (Trust Boundary 2: Docker Internal Bridge Network)
          ▼
[ EVENTRA Modular Monolith (Node.js/TypeScript) ]
     ┌────┴──────────────────────────┐
     │  (Trust Boundary 3: Internal) │  (Trust Boundary 4: External API)
     ▼                               ▼
[ PostgreSQL / Redis ]        [ Stripe / SendGrid APIs ]
```

### D. Major Threat Categories & Baseline Defenses

| Threat Category (STRIDE) | Primary Risk Area | Baseline Defense Mechanism |
| :--- | :--- | :--- |
| **Spoofing** | Attacker impersonates Organizer or Gate Staff. | Strict JWT/Session Auth + Role Guards + MFA ready. |
| **Tampering** | Attacker alters QR payload to create fake tickets. | HMAC-SHA256 cryptographic signature verification. |
| **Repudiation** | Organizer claims they did not delete an event. | Append-only administrative audit log. |
| **Information Disclosure** | PII leak in server log files. | PII redactor middleware in logging framework. |
| **Denial of Service** | Flash-sale bot spamming booking API. | Redis rate limiting + atomic hold timers. |
| **Elevation of Privilege** | Attendee accessing Organizer Dashboard endpoints. | Server-side RBAC middleware on all protected routes. |

---

## 3. Initial Security Checklist

- [ ] `.gitignore` configured to block `.env`, secrets, and certificates.
- [ ] Helmet.js configured for security HTTP headers (CSP, HSTS, X-Frame-Options).
- [ ] Zod schemas implemented for request payload validation.
- [ ] Password hashing configured with Argon2id / bcrypt.
- [ ] Rate limiting middleware enabled for auth endpoints.
- [ ] HMAC signature logic written for QR ticket generation.
- [ ] Automated dependency vulnerability scanning active in CI pipeline.

---

## 4. Security Documentation Structure

```
docs/
└── security/
    ├── 01_SECURITY_FOUNDATION.md  <-- This document
    ├── 02_THREAT_MODEL.md         (Phase 4 Architecture)
    ├── 03_RBAC_SPECIFICATION.md   (Phase 4 Auth)
    └── 04_COMPLIANCE_AND_PRIVACY.md (Phase 7 Deployment)
```

---

## 5. Definition of Done (DoD) — Sub-Phase 1.12
- [x] 16 foundational security and privacy principles defined.
- [x] System Assets, Actors, Trust Boundaries, and STRIDE Threat Categories identified.
- [x] Initial security checklist and privacy considerations documented.
- [x] Security documentation directory structure specified.
- [x] Saved to [`docs/12_SECURITY_AND_PRIVACY_FOUNDATION.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/12_SECURITY_AND_PRIVACY_FOUNDATION.md).
