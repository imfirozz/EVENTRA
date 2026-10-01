# EVENTRA — Payment Failure & Recovery Matrix

---

## 1. Payment Failure Scenario Matrix

| Failure Scenario | Payment State | Booking State | Inventory State | Ticket State | Customer UI Result | Required System Recovery |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **1. Payment Success** | `SUCCEEDED` | `PAID` | Sold (-Qty) | `ISSUED` | "Booking Confirmed" | Fulfill tickets & send email. |
| **2. Card Declined** | `FAILED` | `RESERVED` | Locked (-Qty) | None | "Payment Declined" | Allow retry within remaining 300s timer. |
| **3. User Cancels Payment** | `CANCELLED` | `CANCELLED` | Restored (+Qty) | None | "Payment Cancelled" | Immediately release inventory lock. |
| **4. Gateway Timeout** | `PENDING` | `RESERVED` | Locked (-Qty) | None | "Processing..." | Await webhook; allow retry before 300s. |
| **5. Provider Unavailable** | `FAILED` | `RESERVED` | Locked (-Qty) | None | "Payment Service Busy" | Graceful 503 error; timer continues ticking. |
| **6. Client Disconnect** | `SUCCEEDED` | `PAID` | Sold (-Qty) | `ISSUED` | N/A (Disconnected) | Webhook confirms booking & sends email pass. |
| **7. Duplicate Webhook** | `SUCCEEDED` | `PAID` | Sold (-Qty) | `ISSUED` | "Booking Confirmed" | Idempotency key ignores duplicate; 0 extra tickets. |
| **8. Late Webhook (>300s)** | `SUCCEEDED` | `EXPIRED` | Restored (+Qty) | None | "Hold Expired" | Flag `REFUND_PENDING`; trigger auto-refund. |
| **9. Unknown Gateway State**| `PENDING` | `RESERVED` | Locked (-Qty) | None | "Checking Status..." | Background reconciliation job checks status. |
| **10. Booking Expired** | `EXPIRED` | `EXPIRED` | Restored (+Qty) | None | "Reservation Expired" | Reject checkout; require fresh booking. |
| **11. Refund Failure** | `REFUND_FAILED` | `CANCELLED` | Restored (+Qty) | `CANCELLED` | "Refund Pending" | Log failure to support queue for retry. |

---

## 2. Definition of Done (DoD) — Payment Failure Matrix
- [x] All 11 payment failure scenarios mapped across Payment, Booking, Inventory, Ticket, and Customer UI states.
- [x] Saved to [`docs/requirements/payment-failure-matrix.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/payment-failure-matrix.md).
