# EVENTRA — Venue & Inventory Requirements Specification

---

## 1. Overview & Purpose

This document defines the complete organizer-side requirements for venue configuration and inventory management. It covers venue selection, structural layout (sections, rows, seats, general admission), capacity management, inventory configuration, and the rules governing inventory adjustments. No technical implementation decisions (database schemas, APIs, cloud infrastructure) are prescribed.

---

## 2. Venue Configuration

### 2.1 Venue Information Requirements

| Requirement ID | Requirement                                                                                                                  | Priority | Scope    |
| :------------- | :--------------------------------------------------------------------------------------------------------------------------- | :------- | :------- |
| **VEN-001**    | An organizer must be able to specify a venue for each event (venue name and address).                                        | Must     | MVP      |
| **VEN-002**    | Venue information must include: venue name, address/location, and total capacity.                                            | Must     | MVP      |
| **VEN-003**    | An organizer may optionally provide additional venue details: accessibility information, parking, public transport, contact. | Should   | Advanced |
| **VEN-004**    | An organizer must be able to reuse venue information across multiple events.                                                 | Should   | Advanced |
| **VEN-005**    | Venue information must be displayed on the public event detail page. Consistent with PR-003.                                 | Must     | MVP      |

### 2.2 Venue Structure — General Admission vs Reserved Seating

| Venue Type                 | Description                                                                                   | Scope      |
| :------------------------- | :-------------------------------------------------------------------------------------------- | :--------- |
| **General Admission (GA)** | Quantity-based capacity. Attendees hold unassigned entry rights. No specific seat assignment. | MVP        |
| **Reserved Seating**       | Seat-based capacity. Attendees are assigned specific numbered seats within sections and rows. | Advanced   |
| **Hybrid**                 | Event combines GA areas and reserved seating sections within the same venue.                  | Production |

### 2.3 General Admission Venue Requirements

| Requirement ID | Requirement                                                                                                                  | Priority | Scope |
| :------------- | :--------------------------------------------------------------------------------------------------------------------------- | :------- | :---- |
| **VEN-GA-001** | An organizer must be able to configure a venue as General Admission with a total capacity limit.                             | Must     | MVP   |
| **VEN-GA-002** | Total ticket inventory for a GA event must not exceed the venue's published capacity.                                        | Must     | MVP   |
| **VEN-GA-003** | The system must enforce: $Tickets_{sold} + Holds_{active} \le Capacity_{total}$. Consistent with BRULE-001 and REQ-BOOK-001. | Must     | MVP   |

### 2.4 Reserved Seating Venue Requirements

| Requirement ID | Requirement                                                                                                                                    | Priority | Scope    |
| :------------- | :--------------------------------------------------------------------------------------------------------------------------------------------- | :------- | :------- |
| **VEN-RS-001** | An organizer must be able to configure a venue with sections (e.g., Section A, Section B).                                                     | Should   | Advanced |
| **VEN-RS-002** | Each section may contain rows, and each row may contain numbered seats.                                                                        | Should   | Advanced |
| **VEN-RS-003** | Each seat must have a unique identifier within the venue (e.g., Section A, Row 3, Seat 12).                                                    | Should   | Advanced |
| **VEN-RS-004** | An organizer must be able to mark specific seats as accessible seating (wheelchair, companion).                                                | Should   | Advanced |
| **VEN-RS-005** | Total venue capacity for reserved seating equals the sum of all configured seats across all sections.                                          | Should   | Advanced |
| **VEN-RS-006** | An organizer must not be able to add seats beyond any physical or logical capacity constraints defined for the venue.                          | Should   | Advanced |
| **VEN-RS-007** | Seat states must follow the inventory state machine: `AVAILABLE` → `HELD` → `BOOKED` → `RELEASED`. Consistent with inventory-state-machine.md. | Should   | Advanced |

### 2.5 Accessibility Seating

| Requirement ID  | Requirement                                                                                                                | Priority | Scope    |
| :-------------- | :------------------------------------------------------------------------------------------------------------------------- | :------- | :------- |
| **VEN-ACC-001** | An organizer must be able to designate accessible seating areas or specific accessible seats within a venue configuration. | Should   | Advanced |
| **VEN-ACC-002** | Accessible seating designation must be visible to attendees during seat selection.                                         | Should   | Advanced |
| **VEN-ACC-003** | Accessibility information about the venue (ramps, elevators, accessible restrooms) should be optionally configurable.      | Should   | Advanced |

---

## 3. Inventory Configuration

### 3.1 Inventory Setup Requirements

| Requirement ID | Requirement                                                                                                   | Priority | Scope    |
| :------------- | :------------------------------------------------------------------------------------------------------------ | :------- | :------- |
| **INV-001**    | An organizer must configure total inventory for each event, distributed across one or more ticket categories. | Must     | MVP      |
| **INV-002**    | Total inventory across all ticket categories must not exceed venue capacity.                                  | Must     | MVP      |
| **INV-003**    | Each ticket category must have a defined quantity (number of tickets available for sale).                     | Must     | MVP      |
| **INV-004**    | For General Admission events, inventory is quantity-based (pool of interchangeable units).                    | Must     | MVP      |
| **INV-005**    | For Reserved Seating events, inventory maps to specific seat assignments.                                     | Should   | Advanced |
| **INV-006**    | Inventory must be in a valid state before event publication. See EVT-PUB-005.                                 | Must     | MVP      |

### 3.2 Inventory Availability Rules

| Requirement ID    | Requirement                                                                                                                                                         | Priority | Scope |
| :---------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :------- | :---- |
| **INV-AVAIL-001** | Available inventory must be calculated as: $Stock_{available} = Quantity_{total} - Tickets_{sold} - Holds_{active}$. Consistent with REQ-BOOK-001.                  | Must     | MVP   |
| **INV-AVAIL-002** | Inventory must transition through states: `AVAILABLE` → `HOLD_LOCKED` → `SOLD` or `AVAILABLE` (on expiry/cancellation). Consistent with inventory-state-machine.md. | Must     | MVP   |
| **INV-AVAIL-003** | The system must never allow negative available inventory ($Stock_{available} \ge 0$). Consistent with BRULE-001.                                                    | Must     | MVP   |

### 3.3 Sales Opening & Closing

| Requirement ID   | Requirement                                                                                                                                      | Priority | Scope    |
| :--------------- | :----------------------------------------------------------------------------------------------------------------------------------------------- | :------- | :------- |
| **INV-SALE-001** | An organizer may configure a ticket category availability start date/time (when tickets become available for sale).                              | Should   | Advanced |
| **INV-SALE-002** | An organizer may configure a ticket category availability end date/time (when tickets stop being available for sale).                            | Should   | Advanced |
| **INV-SALE-003** | An organizer must be able to manually close sales for an event (transition to `SALES_CLOSED`).                                                   | Should   | Advanced |
| **INV-SALE-004** | For MVP, all configured ticket categories become available when the event is published and remain available until the event starts or sells out. | Must     | MVP      |

### 3.4 Inventory Adjustment Rules

Inventory adjustments are governed by strict rules to protect existing bookings and reservations.

| Adjustment Type                       | Before Sales | After Reservations Exist                       | After Confirmed Bookings                                 |
| :------------------------------------ | :----------- | :--------------------------------------------- | :------------------------------------------------------- |
| **Increase total capacity**           | ✅ Allowed   | ✅ Allowed                                     | ✅ Allowed                                               |
| **Decrease total capacity**           | ✅ Allowed   | ⚠️ Cannot go below active holds count          | ❌ Cannot go below sold + held. BRULE-009.               |
| **Increase ticket category quantity** | ✅ Allowed   | ✅ Allowed (within total capacity)             | ✅ Allowed (within total capacity)                       |
| **Decrease ticket category quantity** | ✅ Allowed   | ⚠️ Cannot go below reserved count for category | ❌ Cannot go below sold + reserved for category          |
| **Add new ticket category**           | ✅ Allowed   | ✅ Allowed (within remaining capacity)         | ✅ Allowed (within remaining capacity)                   |
| **Remove ticket category**            | ✅ Allowed   | ⚠️ Only if no active holds for category        | ❌ Not allowed if confirmed bookings exist (TKT-CFG-007) |
| **Change seat assignments**           | ✅ Allowed   | ⚠️ Cannot remove held seats                    | ❌ Cannot remove booked seats                            |

### 3.5 Inventory Adjustment Requirements

| Requirement ID  | Requirement                                                                                                                                    | Priority | Scope    |
| :-------------- | :--------------------------------------------------------------------------------------------------------------------------------------------- | :------- | :------- |
| **INV-ADJ-001** | An organizer must be able to increase total event capacity at any time before the event starts (subject to venue constraints).                 | Must     | MVP      |
| **INV-ADJ-002** | The system must reject any capacity decrease that would result in: $NewCapacity < Tickets_{sold} + Holds_{active}$. Consistent with BRULE-009. | Must     | MVP      |
| **INV-ADJ-003** | The system must reject removal of a ticket category with existing confirmed bookings. Consistent with TKT-CFG-007.                             | Must     | MVP      |
| **INV-ADJ-004** | The system must display the current sold and reserved counts when rejecting an inventory decrease.                                             | Must     | MVP      |
| **INV-ADJ-005** | Inventory adjustments must be reflected immediately in real-time availability calculations.                                                    | Must     | MVP      |
| **INV-ADJ-006** | For reserved seating, an organizer must not be able to remove a seat that is currently held or booked.                                         | Should   | Advanced |

---

## 4. Inventory & Venue Consistency Rules

| Rule                         | Description                                                      | Traceability             |
| :--------------------------- | :--------------------------------------------------------------- | :----------------------- |
| **Capacity Invariant**       | Sum of all ticket category quantities ≤ venue capacity.          | BRULE-001, INV-002       |
| **Non-Negativity Invariant** | Available stock ≥ 0 at all times.                                | BRULE-001, INV-AVAIL-003 |
| **Capacity Floor**           | Published capacity cannot be reduced below sold + held count.    | BRULE-009, INV-ADJ-002   |
| **Category Protection**      | Categories with confirmed bookings cannot be removed.            | TKT-CFG-007, INV-ADJ-003 |
| **Seat Integrity**           | Held or booked seats cannot be removed from venue configuration. | VEN-RS-007, INV-ADJ-006  |

---

## 5. MVP vs Advanced vs Production

### 5.1 MVP Venue & Inventory

| Area             | MVP Capability                                                        |
| :--------------- | :-------------------------------------------------------------------- |
| **Venue**        | Simple venue name + address + total capacity. General Admission only. |
| **Inventory**    | Single or multiple ticket categories with quantity-based inventory.   |
| **Availability** | Real-time calculation: $Stock_{available} = Total - Sold - Held$.     |
| **Adjustments**  | Increase capacity anytime; decrease restricted by BRULE-009.          |
| **Sales Window** | Tickets available from publication to event start or sell-out.        |

### 5.2 Advanced Venue & Inventory

| Area              | Advanced Capability                                               |
| :---------------- | :---------------------------------------------------------------- |
| **Venue**         | Sections, rows, numbered seats. Reserved seating support.         |
| **Inventory**     | Seat-based inventory with individual seat states.                 |
| **Accessibility** | Accessible seat designation.                                      |
| **Sales Window**  | Configurable per-category availability dates. Manual sales close. |
| **Adjustments**   | Seat-level protection (no removal of held/booked seats).          |

### 5.3 Production Venue & Inventory

| Area              | Production Capability                                       |
| :---------------- | :---------------------------------------------------------- |
| **Venue**         | Hybrid venues (GA + reserved seating). Venue templates.     |
| **Inventory**     | High-demand flash-sale concurrency handling (REQ-BOOK-004). |
| **Accessibility** | Comprehensive accessibility inventory and compliance.       |
| **Analytics**     | Inventory utilization analytics and forecasting.            |

---

## 6. Open Business & Product Decisions

| Decision ID     | Decision Area            | Description                                                                             |
| :-------------- | :----------------------- | :-------------------------------------------------------------------------------------- |
| **OBD-VEN-001** | Venue Templates          | Whether organizers can save and reuse venue configurations as templates across events.  |
| **OBD-VEN-002** | Venue Change After Sales | Policy for changing venue after tickets are sold (refund, re-assignment, notification). |
| **OBD-INV-001** | Inventory Reallocation   | Whether ticket quantity can be moved between categories after sales begin.              |
| **OBD-INV-002** | Oversold Waitlist        | Whether a waitlist is supported when inventory reaches zero.                            |

---

## 7. Definition of Done — Venue & Inventory Requirements

- [x] Venue information and configuration requirements defined.
- [x] General admission venue requirements defined.
- [x] Reserved seating venue requirements defined.
- [x] Accessibility seating requirements defined.
- [x] Inventory setup and availability rules defined.
- [x] Sales opening/closing requirements defined.
- [x] Inventory adjustment rules and restrictions defined.
- [x] Consistency rules documented.
- [x] MVP vs Advanced vs Production separated.
- [x] Open business decisions catalogued.
- [x] No technical implementation prematurely selected.
- [x] Saved to [`docs/requirements/venue-inventory-requirements.md`](docs/requirements/venue-inventory-requirements.md).
