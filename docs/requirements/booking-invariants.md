# EVENTRA — Booking & Inventory Invariants Specification

---

## 1. Formal System Invariants Directory

| Invariant ID | Invariant Statement | Rationale | Validation Method |
| :--- | :--- | :--- | :--- |
| **INV-001** | Confirmed sold tickets plus active reserved holds shall NEVER exceed published capacity limit ($Stock_{sold} + Holds_{active} \le Capacity$). | Prevents overbooking venue capacity and legal liability. | Test (T) & Analysis (A) |
| **INV-002** | A single reserved seat ID shall have at most ONE active `HELD` or `BOOKED` owner for a specific event. | Prevents double-booking numbered seats. | Test (T) |
| **INV-003** | Expired reservations ($TTL > 300\text{s}$) shall NEVER remain locked or transition to `PAID` without fresh payment authorization. | Restores abandoned inventory to public buyers. | Test (T) |
| **INV-004** | Cancelled orders shall restore inventory stock count back to public available pool if event is active. | Restores stock for legitimate resale. | Test (T) |
| **INV-005** | A booking shall NEVER transition to `PAID` state without a verified payment confirmation transaction ID. | Prevents unpaid ticket issuance. | Test (T) |
| **INV-006** | A single digital QR ticket pass shall be updated to `CHECKED_IN` at most ONCE. | Prevents duplicate door entry using copied passes. | Test (T) |
| **INV-007** | Digital QR ticket payloads shall be cryptographically verifiable via HMAC-SHA256 signature. | Prevents counterfeit ticket creation. | Test (T) |
| **INV-008** | Local storage shall store ZERO credit card numbers, CVVs, or raw cardholder credentials. | Ensures PCI-DSS SAQ A compliance. | Inspection (I) |

---

## 2. Definition of Done (DoD) — Invariants
- [x] All 8 system invariants codified with statements, rationale, and validation methods.
- [x] Saved to [`docs/requirements/booking-invariants.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/booking-invariants.md).
