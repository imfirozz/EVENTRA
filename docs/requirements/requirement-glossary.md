# EVENTRA — Requirements & Domain Terminology Glossary

---

## 1. Terminology Glossary

| Term | Domain Category | Canonical Definition |
| :--- | :--- | :--- |
| **Attendee (Customer)** | User Role | An individual who browses the event catalog, reserves tickets, completes checkout, and presents a digital QR pass at venue entry. |
| **Organizer** | User Role | An event host who creates event listings, configures ticket pricing tiers, sets inventory capacity, and monitors sales analytics. |
| **Gate Inspector (Staff)** | User Role | Venue door personnel authorized to scan attendee digital QR passes using the mobile web scanner app to grant entry. |
| **Super Admin** | User Role | System platform operator responsible for platform health, event moderation, audit trail reviews, and compliance enforcement. |
| **Reservation Hold** | Booking | A temporary lock placed on ticket inventory for 300 seconds (5 mins) during checkout to prevent double-booking. |
| **Available Inventory** | Inventory | Tickets that are unreserved and available for public selection ($Capacity - Reserved - Sold$). |
| **Reserved Inventory** | Inventory | Tickets locked in active 5-minute hold sessions pending checkout payment. |
| **Sold Inventory** | Inventory | Tickets for which payment has been verified and confirmed. |
| **HMAC Signed QR Pass** | Ticket / Security | A digital QR code containing ticket metadata encrypted with a server secret key using HMAC-SHA256 to prevent pass forgery. |
| **Webhook Idempotency** | Payment | The property wherein receiving duplicate payment webhooks produces exactly one ticket fulfillment event without side-effects. |
| **PCI SAQ A** | Security / Payment | Self-Assessment Questionnaire A compliance level where all cardholder data processing is outsourced entirely to a certified payment gateway (Stripe). |
| **Modular Monolith** | Architecture | A software design where all business modules reside in a single codebase and deployment container, separated by strict in-memory domain interfaces. |
| **EARS Syntax** | Requirements | Easy Approach to Requirements Syntax (`WHEN [trigger], THE SYSTEM SHALL [action]`) used for testable requirement phrasing. |

---

## 2. Definition of Done (DoD) — Requirement Glossary
- [x] All 13 core domain terms and technical acronyms defined clearly.
- [x] Saved to [`docs/requirements/requirement-glossary.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/requirement-glossary.md).
