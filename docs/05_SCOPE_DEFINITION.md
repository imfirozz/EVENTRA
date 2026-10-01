# EVENTRA — Scope Definition & Boundary Lock

---

## 1. Feature Classification Matrix

---

### Tier A: MVP Scope (v0.1 – v0.5 Baseline)

| Feature Name | Short Purpose | Why it Belongs in MVP | Dependencies | Required for Initial Release? |
| :--- | :--- | :--- | :--- | :--- |
| **Public Event Catalog & Hero UI** | Showcase upcoming events with filterable cards and responsive details. | Foundation of Task 1 / v0.1 UI baseline. | None | **YES** |
| **User Registration & Auth** | Allow Attendees and Organizers to sign up, log in, and manage profiles. | Mandatory for attributing bookings and organizer permissions. | PostgreSQL Schema | **YES** |
| **Event Creation & Inventory Setup** | Enable Organizers to create events, set dates, and allocate single-tier ticket stock. | Core supply-side requirement for any ticketing app. | User Auth | **YES** |
| **Atomic Ticket Reservation Hold** | Lock a ticket for a fixed duration (5 mins) during checkout. | Prevents overselling during basic checkout. | PostgreSQL / In-Memory Store | **YES** |
| **Simulated / Sandbox Checkout** | Process mock payment transactions to complete booking flows. | Enables end-to-end flow testing without live gateway credentials. | Reservation Engine | **YES** |
| **Basic Digital QR Ticket Generation** | Render a client-side digital QR pass upon successful booking. | Core deliverable for attendee entry verification. | Completed Booking | **YES** |
| **Transactional Email Notifications** | Send basic booking confirmation emails with ticket summary. | Essential post-booking customer communication. | SMTP / Mailer | **YES** |
| **Basic Keyword & Category Filter** | Filter events by title, category (Tech, Workshop), or date. | Basic usability requirement for catalog browsing. | Public Catalog | **YES** |
| **JWT / Session Auth & Role Guards** | Protect organizer routes and user profile endpoints. | Security baseline for multi-role platform. | User Auth | **YES** |
| **Basic Sales Count Summary** | Display total tickets sold vs remaining stock to organizers. | Minimal viability for event management. | Event Creation | **YES** |
| **Single-Instance Monolith & Docker Baseline** | Run Node.js + PostgreSQL + App container cleanly via Docker. | Standardized development environment baseline. | Docker | **YES** |

---

### Tier B: Advanced Scope (v0.8)

| Feature Name | Short Purpose | Why it Belongs in Advanced Tier | Dependencies | Required for Initial Release? |
| :--- | :--- | :--- | :--- | :--- |
| **Multi-Tier Ticket Pricing** | Support Early Bird, General Admission, and VIP ticket tiers. | Essential for realistic event revenue models. | Single-tier Inventory | **NO** |
| **Organizer Event Management Dashboard** | Full UI dashboard for organizers to manage draft, live, and past events. | Elevates platform usability for power organizers. | Event Creation | **NO** |
| **Redis Distributed TTL Hold Locking** | High-performance distributed locks for ticket reservation timers. | Scales hold reservation concurrency beyond single DB instance. | Redis, Booking Engine | **NO** |
| **Stripe / Payment Gateway Integration** | Process real payments via Stripe Checkout & Webhook callbacks. | Production payment validation with real monetary flows. | Sandbox Checkout | **NO** |
| **HMAC-SHA256 Signed QR Tickets** | Encrypt QR payloads with secret signatures to prevent pass forgery. | Prevents counterfeit ticket screenshots at entry. | QR Generation | **NO** |
| **Gate Scanner Web App** | Browser-based camera scanner for staff to check in attendees. | Enables real-world venue entry validation. | Signed QR Tickets | **NO** |
| **Async Background Job Queue** | Process emails and QR generation asynchronously via worker queue (BullMQ). | Offloads I/O heavy tasks from primary HTTP loop. | Redis, SMTP | **NO** |
| **Full-Text & Date Range Search** | Indexed search across event title, description, tags, and location. | Improves discovery experience for large event catalogs. | Basic Keyword Search | **NO** |
| **Rate Limiting & Webhook Signature Guards** | Protect API endpoints from brute force and validate gateway webhooks. | Hardens platform against API abuse and fake webhooks. | Express Middleware, Redis | **NO** |
| **Event Revenue & Sales Tier Analytics** | Graph revenue broken down by ticket tier over time. | Key business insight tool for organizers. | Multi-tier Tickets | **NO** |
| **Redis Caching & GitHub Actions CI** | Cache popular catalog requests; automate test suites on pull requests. | Improves API response times and code quality gatekeeping. | Redis, Docker, GitHub | **NO** |

---

### Tier C: Production / Extreme Scope (v1.0 Hardened)

| Feature Name | Short Purpose | Why it Belongs in Production Tier | Dependencies | Required for Initial Release? |
| :--- | :--- | :--- | :--- | :--- |
| **Platform Admin Governance Dashboard** | Super-admin console to review organizers, flag events, and view audit logs. | Necessary for multi-organizer platform oversight. | User Roles | **NO** |
| **Flash-Sale Concurrency Engine** | Pessimistic DB locks + Redis Lua scripts built for 500+ RPS surges. | Handles high-demand flash sales without deadlocks or overselling. | Redis TTL Locking | **NO** |
| **Offline-Capable Scanner PWA** | Progressive Web App scanner caching validation keys for offline gate scanning. | Ensures gate entry works even when venue Wi-Fi/cellular drops. | Gate Scanner Web App | **NO** |
| **Automated SMS / Push Reminders** | Send event reminders 24 hours prior to event start time. | Increases attendee turnout and engagement. | Async Queue, SMS Gateway | **NO** |
| **Live Gate Scan Analytics Dashboard** | Real-time chart of entry throughput per gate per minute. | Venue crowd management and flow monitoring. | Gate Scanner Web App | **NO** |
| **System Observability & Load Testing Suite** | Prometheus metrics, Grafana dashboards, and k6 load test scripts. | Proves system performance and stability under extreme traffic. | Monolith Core, Docker | **NO** |

---

### Tier D: Explicitly Out of Scope (Scope Boundaries)

| Feature Name | Reason for Exclusion |
| :--- | :--- |
| **Peer-to-Peer Ticket Resale / Scalping** | High legal/regulatory complexity; opens abuse vectors for ticket scalpers. |
| **3D Stadium / Seating Chart CAD Layout Engine** | Massive engineering overhead; does not add core value to tech events/workshops. |
| **In-App Social Messaging & Friend Activity Feeds** | Creeps into social networking; irrelevant to ticket reservation core correctness. |
| **Native Mobile Apps (iOS Swift / Android Kotlin)** | Web responsive / PWA is sufficient for solo developer maintenance. |
| **Custom Physical Scanner Hardware** | Requires embedded hardware engineering; standard smartphone browser camera is sufficient. |
| **Crypto / Multi-Currency Payment Processing** | Volatility and compliance risks; standard card/Stripe gateway is sufficient. |

---

## 2. Scope Freeze Checklist

- [x] Every feature explicitly assigned to MVP, Advanced, Production, or Out-of-Scope.
- [x] Out-of-Scope features clearly documented to reject premature pull requests/ideas.
- [x] Initial release (v0.1 UI baseline -> v0.5 MVP backend) dependencies fully identified.
- [x] Solo developer workload verified as feasible within available timeline.

---

## 3. Definition of Done (DoD) — Sub-Phase 1.5
- [x] Scope boundaries defined across Customer, Organizer, Admin, Booking, Payments, Tickets, Notifications, Search, Real-time, Security, Analytics, and Infrastructure domains.
- [x] Scope Freeze Checklist completed.
- [x] Documentation saved to [`docs/05_SCOPE_DEFINITION.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/05_SCOPE_DEFINITION.md).
