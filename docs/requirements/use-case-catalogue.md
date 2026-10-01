# EVENTRA — Use Case Master Catalogue

---

## 1. Overview & Use Case Index

This document presents the complete **Use Case Index (UC-XXX)** for EVENTRA across Customer, Organizer, Event Staff, Admin, and System domain areas.

---

## 2. Master Use Case Catalogue

| Use Case ID | Use Case Name | Primary Actor | Target Scope | Mapped User Story | Mapped FR |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **UC-001** | Register Customer Account | Customer (Alex) | MVP | US-001 | FR-001 |
| **UC-002** | Authenticate & Log In User | Customer / Organizer | MVP | US-002 | FR-002, FR-004 |
| **UC-003** | Discover & Search Events | Customer (Alex) | MVP | US-003, US-005 | FR-007, FR-009, FR-010 |
| **UC-004** | View Event Details & Availability | Customer (Alex) | MVP | US-004 | FR-008, FR-011 |
| **UC-005** | Reserve Ticket Hold Inventory | Customer (Alex) | MVP | US-006, US-007 | FR-012 |
| **UC-006** | Complete Checkout & Payment | Customer (Alex) | MVP (Sandbox) / Advanced | US-008, US-009, US-010 | FR-014 |
| **UC-007** | Issue & Access Digital Ticket Pass | Customer (Alex) | MVP / Advanced | US-011, US-014 | FR-015, FR-022 |
| **UC-008** | Verify QR Pass at Venue Gate | Staff (Marcus) | Advanced | US-012 | FR-016 |
| **UC-009** | View Customer Order History | Customer (Alex) | MVP | US-013 | FR-017 |
| **UC-010** | Create & Publish Event Listing | Organizer (Sarah) | MVP | US-015 | FR-018, FR-019 |
| **UC-011** | Monitor Organizer Sales Analytics | Organizer (Sarah) | MVP / Advanced | US-016 | FR-020 |
| **UC-012** | Support Order Refund Lookup | Support Operator | Advanced | Support Workflow | FR-023 |
| **UC-013** | Admin Suspend Flagged Event | Super Admin (Devon) | Production | US-017 | FR-024 |

---

## 3. Scope Tier Distribution

- **MVP Use Cases (Tier A Baseline)**: UC-001, UC-002, UC-003, UC-004, UC-005, UC-006 (Sandbox), UC-007, UC-009, UC-010, UC-011 (Sales Count).
- **Advanced Use Cases (Tier B)**: UC-006 (Live Stripe Gateway), UC-007 (HMAC QR Pass), UC-008 (Mobile Gate Scanner App), UC-012 (Support Order Refund Lookup).
- **Production / Extreme Use Cases (Tier C)**: UC-013 (Super-Admin Event Suspension & Audit Logging), Live Gate Scan Throughput Charts.

---

## 4. Definition of Done (DoD) — Use Case Catalogue
- [x] All primary use cases catalogued with scope tiers and traceability metadata.
- [x] Saved to [`docs/requirements/use-case-catalogue.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/use-case-catalogue.md).
