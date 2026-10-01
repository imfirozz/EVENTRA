# EVENTRA — Reliability & Fault Tolerance Specifications

---

## 1. Reliability & Resiliency Architecture

```
[ Application Request ]
         │
         ├──> [ Database Query Pool ] ──(Exhaustion)──> Return 503 Busy + Retry Header
         │
         ├──> [ External Payment Gateway ] ──(Timeout)──> Fallback to Webhook Reconciliation
         │
         └──> [ Async Email Dispatcher ] ──(SMTP Error)──> Queue Retry (BullMQ 3x Exponential Backoff)
```

---

## 2. Fault Tolerance & Failure Recovery Matrix

| System Dependency | Failure Scenario | System Resiliency Behavior | Target Recovery SLA |
| :--- | :--- | :--- | :--- |
| **Payment Gateway** | API network timeout during checkout payment authorization. | System maintains booking in `RESERVED` state within 300s window; relies on asynchronous payment webhook reconciliation. | Webhook processing delay $< 5.0\text{s}$. |
| **Email SMTP Vendor** | Transactional email gateway goes offline or rejects connection. | Asynchronous job queue retains email task and executes 3 retries with exponential backoff ($15\text{s}, 60\text{s}, 300\text{s}$). | Zero lost emails; retried within 15 minutes. |
| **Database Pool** | High concurrency exhausts connection pool connections. | Connection pool queues requests up to 5s timeout; returns structured 503 Service Unavailable error if pool remains full. | Connection queue timeout $< 5.0\text{s}$. |
| **Temporary Network Drop** | Mobile client loses internet during gate check-in scan. | Mobile PWA scanner app caches ticket validation key and queues check-in events locally until network reconnects. | Sync enqueued check-in events within 10s of reconnect. |

---

## 3. Candidate Backup & Disaster Recovery Targets

> [!NOTE] Candidate Targets Requiring Later Infrastructure Validation
> - **Recovery Point Objective (RPO)**: Target $< 24$ hours for daily database snapshots; Target $< 15$ minutes with transaction WAL logging.
> - **Recovery Time Objective (RTO)**: Target $< 4$ hours to restore full containerized database snapshot on fresh VPS.

---

## 4. Definition of Done (DoD) — Reliability
- [x] Resiliency behaviors specified for payment, email, database, and network failures.
- [x] Candidate RPO / RTO targets defined and marked for later infrastructure validation.
- [x] Saved to [`docs/requirements/reliability-requirements.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/reliability-requirements.md).
