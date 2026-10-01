# EVENTRA — Product Capabilities Breakdown

---

## 1. Product Capabilities Matrix Across Tiers

---

### A. MVP Product Capabilities (v0.1 – v0.5 Baseline)

| Domain | Capability Name | Target User Value | Key Feature Deliverable |
| :--- | :--- | :--- | :--- |
| **User Accounts** | Attendee Registration & Login | Secure account access & purchase history tracking. | Email/password sign-up, login, profile view. |
| **Event Discovery** | Public Event Showcase | Browse active event catalog without friction. | Responsive event card grid (Task 1 UI baseline). |
| **Event Details** | Comprehensive Event View | View complete event metadata & schedule. | Dedicated event page with schedule & ticket options. |
| **Search & Filtering** | Keyword & Category Filter | Quickly locate events by name or topic. | Text search bar & category pill buttons. |
| **Ticket Selection** | Ticket Quantity Selector | Choose desired ticket count for booking. | Quantity selector control up to max cap (6). |
| **Availability** | Real-Time Sold-Out Badges | Immediate visual signal when tickets run out. | Dynamic "Sold Out" badge & disabled controls. |
| **Booking** | 5-Minute Hold Reservation | Guaranteed ticket hold timer during checkout. | 300-second reservation hold lock with UI countdown. |
| **Checkout** | Streamlined Checkout Form | Input contact info with clear cost breakdown. | Validated attendee info form with itemized order total. |
| **Payments** | Sandbox Payment Processing | Complete simulated purchases cleanly. | Mock payment gateway authorization. |
| **Confirmation** | Instant Confirmation View | Immediate post-purchase receipt screen. | Order confirmation screen with embedded digital pass. |
| **Digital Tickets** | Mobile Digital Pass | Viewable digital QR pass on mobile screen. | Digital ticket card with rendered QR code image. |
| **History** | Attendee Order Portal | Self-service access to past ticket orders. | Customer order list with re-downloadable tickets. |
| **Notifications** | Transactional Confirmation Email | Instant email proof of purchase. | Asynchronous email receipt dispatch. |
| **Organizer Management** | Event Creation & Publishing | Hosts create and list events live. | Multi-step event creation wizard with capacity bounds. |
| **Organizer Sales** | Basic Ticket Sales Count | High-level sales progress visibility. | Organizer dashboard displaying tickets sold count. |
| **Accessibility** | WCAG 2.1 AA Compliance | Accessible discovery for disabled users. | Keyboard navigation, semantic HTML, high contrast. |
| **Security** | Auth Protection & Role Guards | Prevent unauthorized access & data leakage. | Argon2id hashing, JWT cookies, server-side RBAC guards. |

---

### B. Advanced Product Capabilities (v0.8 Tier)

| Domain | Capability Name | Target User Value | Key Feature Deliverable |
| :--- | :--- | :--- | :--- |
| **Ticket Selection** | Multi-Tier Ticket Pricing | Flexible pricing options (Early Bird, VIP, GA). | Tiered ticket pricing matrix on event page. |
| **Payments** | Live Payment Gateway (Stripe) | Real credit card transaction processing. | Stripe Checkout integration & webhook verification. |
| **Digital Tickets** | HMAC Signed Cryptographic QR | Fraud-proof digital entry passes. | HMAC-SHA256 encrypted QR code payload. |
| **Verification** | Gate Scanner Web Camera App | Sub-second venue door check-in. | Mobile web camera scanner app (Green/Red signal). |
| **Staff Operations** | Gate Inspector Staff Login | Restricted entry validation mode. | Staff authentication & door assignment portal. |
| **Management** | Printable PDF Ticket Download | Physical paper backup pass access. | Formatted A4 PDF ticket document export. |
| **Organizer Tools** | Full Organizer Management Suite | Manage draft, live, & past event portfolio. | Full organizer dashboard with draft/publish toggles. |
| **Organizer Sales** | Revenue & Attendee Guest Roster | Detailed financial & guest insights. | Revenue graphs & exportable attendee list (CSV). |
| **Support** | Support Order Lookup & Refund | Fast dispute resolution & cancellation. | Support transaction search & refund cancellation tool. |
| **Search** | Full-Text Search Indexing | Deep search across description & location. | Indexed search engine filtering. |

---

### C. Production / Extreme Product Capabilities (v1.0 Hardened)

| Domain | Capability Name | Target User Value | Key Feature Deliverable |
| :--- | :--- | :--- | :--- |
| **Verification** | Offline-Capable Scanner Mode | Gate check-in works without venue Wi-Fi. | Progressive Web App scanner caching validation keys. |
| **Administration** | Super-Admin Platform Governance | System moderation & audit oversight. | Super-admin console, user moderation, audit logs. |
| **Analytics** | Live Gate Scan Throughput Charts | Real-time crowd flow monitoring at venue. | Live charts of entry throughput per gate per minute. |
| **Reliability** | High-Throughput Concurrency Engine | System stays up during 500+ RPS flash sales. | Atomic row locking & Redis Lua hold scripts. |
| **Observability** | Full Telemetry & Metrics | Operational transparency for engineering. | Prometheus metrics exporter & Grafana dashboards. |

---

## 2. Definition of Done (DoD) — Product Capabilities
- [x] Product capabilities structured across MVP, Advanced, and Production tiers.
- [x] Target user value and key feature deliverables defined for every capability.
- [x] Saved to [`docs/requirements/product-capabilities.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/product-capabilities.md).
