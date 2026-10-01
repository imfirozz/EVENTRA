# EVENTRA — Security & Privacy Requirements Reference

---

## 1. Security Architecture Baseline Reference

```
[ Incoming Request ]
         │
         ▼
[ Helmet.js Security Headers (CSP, HSTS, X-Frame) ]
         │
         ▼
[ Rate Limiting Middleware (Auth: 5 req/min, Booking: 10 req/min) ]
         │
         ▼
[ Zod Input Schema Sanitization & Parameterized SQL ]
         │
         ▼
[ Authentication & JWT Session Token Validator ]
         │
         ▼
[ Server-Side Role-Based Access Control Guards (RBAC) ]
         │
         ▼
[ Domain Execution & PII Redacted Structured Logging ]
```

---

## 2. Security & Privacy Controls Reference Table

| Control Area | Security Requirement Specification | Verification Method | Target Scope |
| :--- | :--- | :--- | :--- |
| **Password Security** | Passwords hashed using Argon2id or bcrypt (cost $\ge 12$). Zero plain-text passwords stored. | Test (T) & Inspection (I) | **MVP** |
| **Session Security** | Session cookies enforced with `HttpOnly`, `SameSite=Strict`, and `Secure` flags. | Test (T) & Inspection (I) | **MVP** |
| **Role Guards (RBAC)** | Protected routes strictly enforce role permissions (`ATTENDEE`, `ORGANIZER`, `GATE_STAFF`, `SUPER_ADMIN`). | Test (T) | **MVP** |
| **Payment Security** | 100% PCI-DSS SAQ A compliance delegation to Stripe. Zero card data stored locally. | Inspection (I) | **MVP** |
| **Ticket Security** | Digital QR passes HMAC-SHA256 signed using server secret key and event salt. | Test (T) | **Advanced** |
| **Secrets Security** | Secrets injected exclusively via environment variables. Zero hardcoded secrets committed. | Inspection (I) | **MVP** |
| **Logging Security** | Server logs emitted in structured JSON format with automatic PII redaction (passwords, tokens, card fields). | Test (T) & Inspection (I) | **MVP** |
| **Audit Security** | Administrative modifications and gate entry check-ins logged to an append-only audit trail. | Test (T) & Inspection (I) | **Production** |
| **Rate Limiting** | Redis sliding window rate limiting enforced on auth and booking endpoints. | Test (T) | **MVP** |
| **Privacy Minimization**| Collect only essential attendee details (Name, Email). Data anonymized upon erasure request. | Inspection (I) | **MVP** |

---

## 3. Definition of Done (DoD) — Security Reference
- [x] All 10 core security controls specified with concrete verification criteria.
- [x] Saved to [`docs/requirements/security-requirements-reference.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/security-requirements-reference.md).
