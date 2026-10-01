# EVENTRA — Performance SLAs & Load Target Specifications

---

## 1. Response Time Budgets & Performance SLAs

| Operation / Interface | Load Profile | Measurement Metric | Target SLA | Target Status |
| :--- | :--- | :--- | :--- | :--- |
| **Public Event Catalog Landing Page** | Normal Load | LCP / FCP (Throttled 4G) | $\text{LCP} < 1.5\text{s}$, $\text{FCP} < 0.8\text{s}$ | **LOCKED (MVP)** |
| **Event Detail Metadata API** | Normal Load | p95 Latency | $\text{p95} < 100\text{ms}$ | **LOCKED (MVP)** |
| **Catalog Search & Filtering** | Normal Load | Response Latency | $< 100\text{ms}$ | **LOCKED (MVP)** |
| **Atomic Reservation Hold Lock API** | Peak Load Surge | p95 Latency | $\text{p95} < 150\text{ms}$ | **LOCKED (MVP)** |
| **Checkout Form Submission** | Normal Load | Response Latency | $< 150\text{ms}$ | **LOCKED (MVP)** |
| **Payment Confirmation Webhook** | Peak Load Surge | Webhook Response Time | $< 200\text{ms}$ | **LOCKED (MVP)** |
| **Gate QR Scanner Validation** | Mobile Network | End-to-End Camera to UI | $< 1.0\text{s}$ (Target $< 500\text{ms}$) | **LOCKED (Advanced)** |
| **Async Background Email Dispatch** | Peak Load | Queue Processing Delay | $< 5.0\text{s}$ | **LOCKED (MVP)** |

---

## 2. System Load Profiles & Capacity Targets

### A. Normal Load Profile (Baseline Operating Conditions)
- **Concurrent Active Users**: 50 simultaneous users.
- **System Throughput**: 10 Requests Per Second (RPS).
- **Target Latency**: p95 $< 100\text{ms}$.
- **Target CPU / RAM Utilization**: $< 20\%$ node resources.

### B. Peak Load Profile (High-Demand Event Announcement Surge)
- **Concurrent Active Users**: 500 simultaneous users.
- **System Throughput**: 100 Requests Per Second (RPS).
- **Target Latency**: p95 $< 300\text{ms}$.
- **Target CPU / RAM Utilization**: $< 60\%$ node resources.

### C. Stress & Load-Testing Target Profile (Flash-Sale Simulation Target)
- **Concurrent Active Users**: 2,000 simultaneous users.
- **System Throughput Target**: 500 Requests Per Second (RPS).
- **Target Database Status**: 0 deadlocks; 0 unhandled 500 server errors; 0 overbooked tickets.
- **Validation Schedule**: *Target to be validated during performance engineering load tests in Phase 6*.

---

## 3. Definition of Done (DoD) — Performance
- [x] Response time budgets specified for catalog, search, reservation, checkout, QR validation, and background queues.
- [x] Normal, Peak, and Stress load profiles defined.
- [x] Saved to [`docs/requirements/performance-requirements.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/performance-requirements.md).
