# EVENTRA — User Stories Catalogue

---

## 1. Overview

This document specifies the complete **User Story Catalogue (US-XXX)** for EVENTRA. Every story represents tangible user value phrased using the standard template:
`As a [persona], I want [capability], so that [value/outcome].`

---

## 2. Complete User Story Catalogue

---

### Epic 1: Account & Identity

#### US-001: Attendee Account Registration
- **Story ID**: US-001
- **Epic**: Account & Identity (EPC-01)
- **Persona**: Customer (Alex the Attendee)
- **User Story**: As a Customer, I want to create an account using my name, email, and password, so that I can securely store my ticket purchases and access them anytime.
- **Related BR**: BR-009, BR-016 | **Related PR**: PR-001 | **Related FR**: FR-001
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: None
- **Business Value**: Enables user-to-order attribution and reduces lost ticket support requests.
- **Edge Cases**: Registering with an already existing email address.
- **Acceptance Criteria Reference**: FR-001 Acceptance Condition.
- **Definition of Done**: Account record created in database, password hashed with Argon2id, authenticated session token returned.

---

#### US-002: User Account Login
- **Story ID**: US-002
- **Epic**: Account & Identity (EPC-01)
- **Persona**: Customer (Alex) / Organizer (Sarah)
- **User Story**: As a Registered User, I want to log into my account with my email and password, so that I can access my personalized dashboard and ticket passes.
- **Related BR**: BR-009, BR-016 | **Related PR**: PR-001 | **Related FR**: FR-002
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: US-001
- **Business Value**: Protects private ticket orders and organizer management tools behind authentication.
- **Edge Cases**: Submitting incorrect password multiple times.
- **Acceptance Criteria Reference**: FR-002 Acceptance Condition.
- **Definition of Done**: Valid credentials issue JWT session token; invalid credentials return 401 error.

---

### Epic 2: Event Discovery

#### US-003: Public Event Catalog Showcase View
- **Story ID**: US-003
- **Epic**: Event Discovery (EPC-02)
- **Persona**: Customer (Alex the Attendee)
- **User Story**: As a Customer, I want to view a public catalog of upcoming technical events, so that I can discover interesting conferences and workshops without logging in first.
- **Related BR**: BR-001, BR-020 | **Related PR**: PR-002 | **Related FR**: FR-007
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: None
- **Business Value**: Removes discovery friction for prospective event attendees.
- **Edge Cases**: Zero active events in the catalog (display empty state).
- **Acceptance Criteria Reference**: FR-007 Acceptance Condition.
- **Definition of Done**: Catalog renders event cards with title, date, venue, banner, and starting price across mobile/desktop viewports.

---

### Epic 3: Event Details

#### US-004: Event Detail Page View
- **Story ID**: US-004
- **Epic**: Event Details (EPC-03)
- **Persona**: Customer (Alex the Attendee)
- **User Story**: As a Customer, I want to click an event card to view full event descriptions, speaker schedules, venue address, and ticket options, so that I can decide whether to attend.
- **Related BR**: BR-002, BR-003 | **Related PR**: PR-003 | **Related FR**: FR-008
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: US-003
- **Business Value**: Provides all necessary information to drive ticket purchasing decisions.
- **Edge Cases**: Event has sold out all ticket stock.
- **Acceptance Criteria Reference**: FR-008 Acceptance Condition.
- **Definition of Done**: Dedicated page renders full event metadata and ticket selection controls.

---

### Epic 4: Search & Filtering

#### US-005: Keyword & Category Event Filtering
- **Story ID**: US-005
- **Epic**: Search & Filtering (EPC-04)
- **Persona**: Customer (Alex the Attendee)
- **User Story**: As a Customer, I want to filter the event catalog by category tags (e.g. Workshop, Meetup) or search by keyword, so that I can quickly find events relevant to my interests.
- **Related BR**: BR-015 | **Related PR**: PR-004 | **Related FR**: FR-009, FR-010
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: US-003
- **Business Value**: Improves event discovery for large event catalogs.
- **Edge Cases**: Search text returns zero matching events.
- **Acceptance Criteria Reference**: FR-009, FR-010 Acceptance Condition.
- **Definition of Done**: Typing query or selecting category tag instantly updates displayed event cards.

---

### Epic 5: Ticket Selection

#### US-006: Select Ticket Quantity
- **Story ID**: US-006
- **Epic**: Ticket Selection (EPC-05)
- **Persona**: Customer (Alex the Attendee)
- **User Story**: As a Customer, I want to select my desired ticket category and quantity (up to 6 tickets), so that I can purchase tickets for myself and my colleagues.
- **Related BR**: BR-003, BR-004 | **Related PR**: PR-005 | **Related FR**: FR-011
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: US-004
- **Business Value**: Enables group ticket purchases while enforcing business order caps.
- **Edge Cases**: User attempts to select 7 tickets (capped at 6).
- **Acceptance Criteria Reference**: FR-011 Acceptance Condition.
- **Definition of Done**: Quantity selector updates subtotal and caps selection at 6 tickets or remaining stock.

---

### Epic 6: Booking Engine (Critical Booking Core)

#### US-007: Temporary Ticket Reservation Hold
- **Story ID**: US-007
- **Epic**: Booking Engine (EPC-06)
- **Persona**: Customer (Alex the Attendee)
- **User Story**: As a Customer, I want my selected tickets to be temporarily reserved for 5 minutes when I click checkout, so that no one else can buy them while I enter my payment details.
- **Related BR**: BR-004, BR-005 | **Related PR**: PR-007 | **Related FR**: FR-012
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: US-006
- **Business Value**: Prevents phantom availability and double-booking during ticket checkout.
- **Edge Cases**: Concurrent users click "Reserve" on the last remaining ticket simultaneously.
- **Acceptance Criteria Reference**: FR-012 Acceptance Condition.
- **Definition of Done**: Clicking reserve locks stock atomically for 300s and displays a real-time countdown timer.

---

#### US-008: Reservation Hold Expiration Alert
- **Story ID**: US-008
- **Epic**: Booking Engine (EPC-06)
- **Persona**: Customer (Alex the Attendee)
- **User Story**: As a Customer, I want to see a clear countdown timer during checkout and be notified if my reservation expires, so that I know whether my hold is still active.
- **Related BR**: BR-005 | **Related PR**: PR-007 | **Related FR**: FR-013
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: US-007
- **Business Value**: Gives transparent feedback and returns uncompleted stock to public availability.
- **Edge Cases**: User submits payment 1 second after timer hits 0:00.
- **Acceptance Criteria Reference**: FR-013 Acceptance Condition.
- **Definition of Done**: When timer hits 0:00, booking transitions to `EXPIRED` and stock returns to public catalog.

---

### Epic 7: Checkout

#### US-009: Attendee Checkout Information Input
- **Story ID**: US-009
- **Epic**: Checkout (EPC-07)
- **Persona**: Customer (Alex the Attendee)
- **User Story**: As a Customer, I want to enter my name and email on a clean checkout form with a clear order total breakdown, so that I can review my purchase before authorizing payment.
- **Related BR**: BR-005, BR-020 | **Related PR**: PR-008 | **Related FR**: FR-014
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: US-007
- **Business Value**: Ensures accurate attendee ticket registration and price transparency.
- **Edge Cases**: Entering malformed email address syntax.
- **Acceptance Criteria Reference**: FR-014 Acceptance Condition.
- **Definition of Done**: Checkout form validates contact fields and presents itemized cost breakdown.

---

### Epic 8: Payments (Critical Payment Core)

#### US-010: Complete Ticket Purchase Payment
- **Story ID**: US-010
- **Epic**: Payments (EPC-08)
- **Persona**: Customer (Alex the Attendee)
- **User Story**: As a Customer, I want to complete my payment securely, so that my ticket reservation is converted into a confirmed paid order.
- **Related BR**: BR-006, BR-016 | **Related PR**: PR-009 | **Related FR**: FR-014
- **Priority**: Must (Sandbox MVP) / Should (Live Stripe Advanced) | **Scope**: MVP / Advanced
- **Dependencies**: US-009
- **Business Value**: Reconciles financial payment authorization with ticket order fulfillment.
- **Edge Cases**: Card declined by bank during authorization.
- **Acceptance Criteria Reference**: FR-014 Acceptance Condition.
- **Definition of Done**: Payment confirmation transitions booking to `PAID` and triggers ticket pass generation.

---

### Epic 9: Digital Tickets (Critical Ticket Core)

#### US-011: Access Mobile Digital QR Ticket Pass
- **Story ID**: US-011
- **Epic**: Digital Tickets (EPC-09)
- **Persona**: Customer (Alex the Attendee)
- **User Story**: As a Customer, I want to access my digital ticket pass with an embedded QR code on my smartphone screen after purchasing, so that I can present it at the venue door for entry.
- **Related BR**: BR-007 | **Related PR**: PR-011 | **Related FR**: FR-015
- **Priority**: Must (Basic QR) / Should (HMAC Signed QR) | **Scope**: MVP / Advanced
- **Dependencies**: US-010
- **Business Value**: Eliminates paper pass printing requirements and enables instant door scanning.
- **Edge Cases**: Displaying ticket pass on a device without active cellular internet connection.
- **Acceptance Criteria Reference**: FR-015 Acceptance Condition.
- **Definition of Done**: Paid booking screen renders digital pass with event details and valid QR code image.

---

### Epic 10: Ticket Verification

#### US-012: Scan & Validate Ticket QR Pass at Venue Gate
- **Story ID**: US-012
- **Epic**: Ticket Verification (EPC-10)
- **Persona**: Event Staff (Marcus the Gate Inspector)
- **User Story**: As a Gate Inspector, I want to scan attendee QR codes using my mobile phone camera and see an immediate visual validation signal (Green Valid vs Red Invalid/Already Used), so that I can quickly admit attendees at the door.
- **Related BR**: BR-008, BR-012 | **Related PR**: PR-012, PR-022 | **Related FR**: FR-016
- **Priority**: Should | **Scope**: Advanced
- **Dependencies**: US-011
- **Business Value**: Enables sub-second attendee door check-in and prevents duplicate ticket pass reuse.
- **Edge Cases**: Scanning a ticket pass that was already scanned 10 minutes earlier.
- **Acceptance Criteria Reference**: FR-016 Acceptance Condition.
- **Definition of Done**: Valid QR scan updates ticket status to `CHECKED_IN` and displays Green screen; duplicate scan displays Red "ALREADY USED" screen.

---

### Epic 11: Customer Account & Booking History

#### US-013: View Past & Active Ticket Purchases
- **Story ID**: US-013
- **Epic**: Customer Account & Booking History (EPC-11)
- **Persona**: Customer (Alex the Attendee)
- **User Story**: As a Customer, I want to log into my account and view a list of all my active and past ticket purchases, so that I can easily find my digital QR passes whenever I need them.
- **Related BR**: BR-009 | **Related PR**: PR-013 | **Related FR**: FR-017
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: US-002, US-010
- **Business Value**: Provides self-service ticket pass access, reducing support inquiries.
- **Edge Cases**: Customer has no past ticket purchases (render friendly empty state).
- **Acceptance Criteria Reference**: FR-017 Acceptance Condition.
- **Definition of Done**: Account order history list displays all past orders with clickable "View Pass" links.

---

### Epic 13: Notifications

#### US-014: Receive Order Confirmation Email
- **Story ID**: US-014
- **Epic**: Notifications (EPC-13)
- **Persona**: Customer (Alex the Attendee)
- **User Story**: As a Customer, I want to receive an immediate email confirmation with my order summary and a link to my digital ticket after paying, so that I have permanent proof of purchase.
- **Related BR**: BR-014 | **Related PR**: PR-016 | **Related FR**: FR-022
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: US-010
- **Business Value**: Provides written order proof and accessible ticket links in attendee inbox.
- **Edge Cases**: Email delivery fails temporarily due to SMTP network error (retry background job).
- **Acceptance Criteria Reference**: FR-022 Acceptance Condition.
- **Definition of Done**: Booking `PAID` state transition enqueues background email dispatch job.

---

### Epic 15: Event Management (Organizer Core)

#### US-015: Create & Publish Event Listing
- **Story ID**: US-015
- **Epic**: Event Management (EPC-15)
- **Persona**: Event Organizer (Sarah the Host)
- **User Story**: As an Event Organizer, I want to fill out an event creation form with title, description, date, location, banner image, ticket capacity, and ticket price, so that I can publish my event to the catalog.
- **Related BR**: BR-010 | **Related PR**: PR-018, PR-019 | **Related FR**: FR-018, FR-019
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: US-002
- **Business Value**: Empowers event hosts to onboard supply and sell tickets on EVENTRA.
- **Edge Cases**: Setting event start date in the past or capacity $=0$.
- **Acceptance Criteria Reference**: FR-018, FR-019 Acceptance Condition.
- **Definition of Done**: Organizer can create and publish an event, rendering it immediately on the public event catalog.

---

### Epic 17: Organizer Dashboard

#### US-016: Monitor Event Sales Count
- **Story ID**: US-016
- **Epic**: Organizer Dashboard (EPC-17)
- **Persona**: Event Organizer (Sarah the Host)
- **User Story**: As an Event Organizer, I want to view a dashboard showing how many tickets have been sold versus remaining stock for my published events, so that I can track sales progress.
- **Related BR**: BR-011 | **Related PR**: PR-020, PR-021 | **Related FR**: FR-020
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: US-015
- **Business Value**: Gives organizers real-time commercial visibility into ticket sales performance.
- **Edge Cases**: Event has 0 ticket sales.
- **Acceptance Criteria Reference**: FR-020 Acceptance Condition.
- **Definition of Done**: Dashboard displays accurate count of sold tickets and remaining inventory per event.

---

### Epic 20: Administration

#### US-017: Super-Admin Suspend Flagged Event
- **Story ID**: US-017
- **Epic**: Administration (EPC-20)
- **Persona**: Platform Administrator (Devon the Admin)
- **User Story**: As a Super Admin, I want to suspend a fraudulent or policy-violating event listing, so that it is immediately hidden from the public catalog and logged to the audit trail.
- **Related BR**: BR-013 | **Related PR**: PR-023 | **Related FR**: FR-024
- **Priority**: Could | **Scope**: Production
- **Dependencies**: US-015
- **Business Value**: Protects platform safety, user trust, and regulatory compliance.
- **Edge Cases**: Non-admin user attempts to access administrative moderation API.
- **Acceptance Criteria Reference**: FR-024 Acceptance Condition.
- **Definition of Done**: Admin suspension action updates event state to `SUSPENDED`, hides it from catalog, and appends audit log record.

---

## 3. Definition of Done (DoD) — User Stories
- [x] User stories created across primary Epics using standard `As a... I want... so that...` syntax.
- [x] Persona, Epic, Metadata, Dependencies, Business Value, Edge Cases, and DoD defined for every story.
- [x] Saved to [`docs/requirements/user-stories.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/user-stories.md).
