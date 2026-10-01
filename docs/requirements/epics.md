# EVENTRA — Master Epic Catalogue

---

## 1. Overview & Epic Structure

This document defines the 25 primary **Epics** for EVENTRA. Each epic represents a high-level feature group containing granular, implementable User Stories (US-XXX) mapped directly to approved Business, Product, and Functional Requirements.

---

## 2. Master Epic Index

| Epic ID | Epic Name | Description | Target Scope |
| :--- | :--- | :--- | :--- |
| **EPC-01** | Account & Identity | User registration, login, logout, and profile management. | MVP |
| **EPC-02** | Event Discovery | Public event catalog showcase and landing page rendering. | MVP |
| **EPC-03** | Event Details | Comprehensive event detail view, metadata, and schedule display. | MVP |
| **EPC-04** | Search & Filtering | Keyword search, category filtering, and date filtering. | MVP / Advanced |
| **EPC-05** | Ticket Selection | Single and multi-tier ticket selection and quantity caps. | MVP / Advanced |
| **EPC-06** | Booking Engine | Atomic reservation hold locks and 5-minute countdown timers. | MVP |
| **EPC-07** | Checkout | Attendee contact input form and order summary display. | MVP |
| **EPC-08** | Payments | Payment authorization, webhook reconciliation, and refund handling. | MVP / Advanced |
| **EPC-09** | Digital Tickets | Digital QR ticket pass rendering and PDF pass export. | MVP / Advanced |
| **EPC-10** | Ticket Verification | Door camera QR scanning and single-entry validation. | Advanced |
| **EPC-11** | Customer History | Attendee order history portal and past pass retrieval. | MVP |
| **EPC-12** | Ticket Transfer | Peer-to-peer ticket reselling/transfer (EXPLICIT OUT OF SCOPE). | Out of Scope |
| **EPC-13** | Notifications | Asynchronous transactional confirmation email dispatch. | MVP |
| **EPC-14** | Organizer Onboarding| Organizer registration and profile setup. | MVP |
| **EPC-15** | Event Management | Event creation wizard, drafting, editing, and publishing. | MVP |
| **EPC-16** | Inventory & Pricing | Ticket tier pricing setup and venue capacity allocation. | MVP / Advanced |
| **EPC-17** | Organizer Dashboard | Centralized organizer portfolio view and sales tracking. | MVP |
| **EPC-18** | Attendee Management | Guest roster visualization and attendee export tools. | Advanced |
| **EPC-19** | Event Staff | Door inspector staff access and scanning mode authorization. | Advanced |
| **EPC-20** | Administration | Super-admin moderation, event suspension, and audit logs. | Production |
| **EPC-21** | Reporting & Analytics| Live gate scan throughput charts and revenue reporting. | Advanced / Production |
| **EPC-22** | Support & Operations | Support transaction search, order lookup, and cancellations. | Advanced |
| **EPC-23** | Security Protection| Argon2id password hashing, JWT cookies, and RBAC guards. | MVP |
| **EPC-24** | Accessibility | WCAG 2.1 AA accessibility and keyboard navigation. | MVP |
| **EPC-25** | Platform Reliability | Webhook idempotency, failure isolation, and rate limiting. | MVP |

---

## 3. Definition of Done (DoD) — Epics
- [x] All 25 epics defined and mapped to target scope tiers.
- [x] Saved to [`docs/requirements/epics.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/epics.md).
