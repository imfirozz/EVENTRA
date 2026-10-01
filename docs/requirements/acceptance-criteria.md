# EVENTRA — Acceptance Criteria Specification (ACS)

---

## 1. Overview & Formatting Standard

This document specifies the testable **Acceptance Criteria (AC-XXX)** for EVENTRA using the standard **Given / When / Then** BDD syntax. Acceptance criteria define the exact observable conditions under which a User Story, Use Case, or Functional Requirement is considered complete and successfully verified.

---

## 2. Master Acceptance Criteria Catalogue

---

### Domain 1: Account Registration & Authentication

#### AC-001: Successful Customer Registration
- **Criteria ID**: AC-001
- **Title**: Successful Customer Registration
- **Related Requirements**: US-001, FR-001, PR-001, BR-009, UC-001
- **Given**: An unregistered user is on the registration view with valid input data (Name: "Alex", Email: "alex@example.com", Password: "Password123").
- **When**: The user submits the registration form.
- **Then**: The system shall create an Attendee account record with an Argon2id/bcrypt salted password hash.
- **And**: The system shall return an HTTP 200 response containing an authenticated session token.
- **Scenario Type**: Positive
- **Validation Type**: API / Integration Test
- **Scope**: MVP

---

#### AC-002: Duplicate Email Registration Prevention (Negative Test)
- **Criteria ID**: AC-002
- **Title**: Duplicate Email Registration Prevention
- **Related Requirements**: US-001, FR-001, PR-001, BR-009, UC-001
- **Given**: A user account already exists with email "registered@example.com".
- **When**: A new user attempts to submit a registration form using "registered@example.com".
- **Then**: The system shall reject the request and return an HTTP 400 Validation Error.
- **And**: The response shall state: "Email address is already registered."
- **Scenario Type**: Negative
- **Validation Type**: Integration Test
- **Scope**: MVP

---

#### AC-003: Invalid Credentials Login Rejection (Negative Test)
- **Criteria ID**: AC-003
- **Title**: Invalid Credentials Login Rejection
- **Related Requirements**: US-002, FR-002, PR-001, BR-009, UC-002
- **Given**: A user is on the login view.
- **When**: The user submits invalid email or password credentials.
- **Then**: The system shall reject authentication and return an HTTP 401 Unauthorized response.
- **And**: The response message shall state: "Invalid email or password" without indicating which specific credential field was incorrect.
- **Scenario Type**: Negative / Security
- **Validation Type**: Security / API Test
- **Scope**: MVP

---

### Domain 2: Event Discovery & Search

#### AC-004: Public Event Catalog Showcase Display
- **Criteria ID**: AC-004
- **Title**: Public Event Catalog Showcase Display
- **Related Requirements**: US-003, FR-007, PR-002, BR-001, UC-003
- **Given**: An unauthenticated visitor navigates to the public homepage.
- **When**: The public catalog page loads.
- **Then**: The system shall render a responsive grid of published event cards.
- **And**: Each event card shall display title, date/time, venue address, banner image, and starting ticket price.
- **Scenario Type**: Positive
- **Validation Type**: End-to-End / Manual Verification
- **Scope**: MVP

---

#### AC-005: Category Tag Catalog Filtering
- **Criteria ID**: AC-005
- **Title**: Category Tag Catalog Filtering
- **Related Requirements**: US-005, FR-010, PR-004, BR-015, UC-003
- **Given**: The public event catalog contains events assigned to "Workshop" and "Conference" categories.
- **When**: A user clicks the "Workshop" category filter button.
- **Then**: The system shall update the catalog display to render only events assigned to the "Workshop" category.
- **And**: All "Conference" event cards shall be hidden.
- **Scenario Type**: Positive
- **Validation Type**: End-to-End Test
- **Scope**: MVP

---

### Domain 3: Ticket Selection & Availability

#### AC-006: Sold-Out Stock Availability Indicator (Boundary Condition)
- **Criteria ID**: AC-006
- **Title**: Sold-Out Stock Availability Indicator
- **Related Requirements**: US-006, FR-011, PR-006, BR-004, UC-004
- **Given**: An event ticket tier has remaining available stock equal to 0.
- **When**: A user loads the event detail page.
- **Then**: The system shall display a prominent "Sold Out" badge on the ticket tier.
- **And**: The ticket quantity selector and "Reserve Tickets" button shall be disabled.
- **Scenario Type**: Boundary / Negative
- **Validation Type**: API / Visual Verification
- **Scope**: MVP

---

#### AC-007: Maximum Ticket Order Cap Enforcement (Boundary Condition)
- **Criteria ID**: AC-007
- **Title**: Maximum Ticket Order Cap Enforcement
- **Related Requirements**: US-006, FR-011, PR-005, BR-003, UC-004
- **Given**: A ticket tier has 20 tickets available.
- **When**: A user opens the quantity selection control.
- **Then**: The system shall cap the selectable ticket quantity at a maximum of 6 tickets per transaction.
- **And**: Selecting 6 tickets shall set the subtotal to 6 times the single ticket price.
- **Scenario Type**: Boundary
- **Validation Type**: Integration Test
- **Scope**: MVP

---

### Domain 4: Booking Engine & Concurrency Control (Critical Booking Core)

#### AC-008: 5-Minute Atomic Ticket Reservation Hold Lock
- **Criteria ID**: AC-008
- **Title**: 5-Minute Atomic Ticket Reservation Hold Lock
- **Related Requirements**: US-007, FR-012, PR-007, BR-004, BR-005, UC-005
- **Given**: An event ticket tier has 5 available tickets.
- **When**: A logged-in customer selects 2 tickets and clicks "Reserve Tickets".
- **Then**: The system shall atomically lock 2 tickets in a `RESERVED` booking state.
- **And**: Available public stock shall immediately decrement from 5 to 3.
- **And**: The client checkout view shall display a running 300-second (5-minute) countdown timer.
- **Scenario Type**: Positive / Concurrency
- **Validation Type**: Concurrency / Integration Test
- **Scope**: MVP

---

#### AC-009: 5-Minute Hold Timer Expiration & Stock Recovery
- **Criteria ID**: AC-009
- **Title**: 5-Minute Hold Timer Expiration & Stock Recovery
- **Related Requirements**: US-008, FR-013, PR-007, BR-005, UC-005
- **Given**: A customer has an active booking in `RESERVED` state with 2 locked tickets.
- **When**: The 300-second reservation timer reaches 0:00 without payment verification.
- **Then**: The system shall transition the booking state from `RESERVED` to `EXPIRED`.
- **And**: The 2 locked tickets shall be automatically restored to public available stock.
- **And**: The checkout screen shall display an alert: "Reservation Expired. Please select tickets again."
- **Scenario Type**: Negative / Timer Boundary
- **Validation Type**: Integration Test
- **Scope**: MVP

---

#### AC-010: Two Customers Competing for Final Available Ticket (Concurrency Test)
- **Criteria ID**: AC-010
- **Title**: Two Customers Competing for Final Available Ticket
- **Related Requirements**: US-007, FR-012, PR-006, BR-004, UC-005
- **Given**: An event ticket tier has exactly 1 remaining available ticket ($Stock_{available} = 1$).
- **When**: Customer A and Customer B submit reservation requests for 1 ticket simultaneously.
- **Then**: The system shall grant a confirmed 300-second `RESERVED` hold to exactly ONE customer.
- **And**: The second customer's request shall be rejected with an HTTP 409 Conflict response: "Tickets no longer available."
- **And**: Total tickets reserved or sold for the tier shall equal exactly 1 (0% overselling rate).
- **Scenario Type**: Concurrency / Critical Boundary
- **Validation Type**: Concurrency Test
- **Scope**: MVP

---

### Domain 5: Payments & Order Fulfillment (Critical Payment Core)

#### AC-011: Verified Payment Order Fulfillment
- **Criteria ID**: AC-011
- **Title**: Verified Payment Order Fulfillment
- **Related Requirements**: US-010, FR-014, PR-009, PR-010, BR-006, UC-006
- **Given**: A customer has an active booking in `RESERVED` state with 2 tickets.
- **When**: The payment authorization succeeds and is verified by the system.
- **Then**: The system shall atomically update booking state from `RESERVED` to `PAID`.
- **And**: The system shall generate 2 digital ticket records containing unique ticket IDs.
- **And**: The system shall enqueue an asynchronous confirmation email job.
- **And**: The user interface shall redirect to the Order Confirmation view displaying order details.
- **Scenario Type**: Positive
- **Validation Type**: Integration / API Test
- **Scope**: MVP (Sandbox) / Advanced (Live Gateway)

---

#### AC-012: Payment Gateway Declined Card Handling (Negative Test)
- **Criteria ID**: AC-012
- **Title**: Payment Gateway Declined Card Handling
- **Related Requirements**: US-010, FR-014, PR-009, BR-006, UC-006
- **Given**: A customer has an active `RESERVED` booking with 180 seconds remaining on the hold timer.
- **When**: The customer submits a payment card that is declined by the bank.
- **Then**: The booking state shall remain `RESERVED` with the hold timer continuing to count down.
- **And**: The checkout view shall display an error message: "Payment Declined. Please try another card."
- **And**: The customer can re-submit valid card details within the remaining 180 seconds.
- **Scenario Type**: Negative
- **Validation Type**: Integration Test
- **Scope**: MVP

---

#### AC-013: Duplicate Payment Webhook Idempotency Processing
- **Criteria ID**: AC-013
- **Title**: Duplicate Payment Webhook Idempotency Processing
- **Related Requirements**: US-010, FR-014, PR-009, BR-006, BR-019, UC-006
- **Given**: A booking has already transitioned to `PAID` state following a confirmed payment webhook.
- **When**: The payment gateway re-sends an identical payment webhook payload with the same transaction ID.
- **Then**: The system shall detect the duplicate transaction ID and process the payload idempotently.
- **And**: The system shall return HTTP 200 OK to the gateway without generating duplicate tickets or secondary emails.
- **Scenario Type**: Reliability / Concurrency
- **Validation Type**: Integration Test
- **Scope**: MVP

---

### Domain 6: Digital Tickets & Gate Verification (Critical Ticket Core)

#### AC-014: Digital QR Pass Display for Paid Order
- **Criteria ID**: AC-014
- **Title**: Digital QR Pass Display for Paid Order
- **Related Requirements**: US-011, FR-015, PR-011, BR-007, UC-007
- **Given**: A customer has a confirmed booking in `PAID` state.
- **When**: The customer views their order confirmation or account order history.
- **Then**: The system shall render a digital ticket pass displaying event title, date, attendee name, seat tier, ticket ID, and a clear QR code image.
- **Scenario Type**: Positive
- **Validation Type**: End-to-End / Visual Verification
- **Scope**: MVP (Basic QR) / Advanced (HMAC QR)

---

#### AC-015: Valid QR Pass Door Gate Scan Check-In
- **Criteria ID**: AC-015
- **Title**: Valid QR Pass Door Gate Scan Check-In
- **Related Requirements**: US-012, FR-016, PR-012, BR-008, UC-008
- **Given**: An authenticated Gate Inspector is operating the mobile gate scanner app for a live event.
- **When**: The staff camera scans a valid, un-scanned attendee QR code with status `PAID`.
- **Then**: The system shall verify HMAC signature validity and atomically update ticket status: `PAID` $\rightarrow$ `CHECKED_IN`.
- **And**: The scanner app screen shall display a full-screen Green "VALID — Check-In Complete" visual response.
- **Scenario Type**: Positive
- **Validation Type**: Integration / E2E Test
- **Scope**: Advanced

---

#### AC-016: Rescanning Already-Used Ticket Pass (Negative Test)
- **Criteria ID**: AC-016
- **Title**: Rescanning Already-Used Ticket Pass
- **Related Requirements**: US-012, FR-016, PR-012, BR-008, UC-008
- **Given**: A digital ticket pass has already been scanned and holds status `CHECKED_IN`.
- **When**: The gate scanner scans the same QR pass a second time.
- **Then**: The system shall reject the check-in attempt.
- **And**: The scanner app screen shall display a full-screen Red "ALREADY USED" warning screen showing the original check-in timestamp.
- **And**: Ticket status shall remain `CHECKED_IN` without creating duplicate check-in events.
- **Scenario Type**: Negative / Security
- **Validation Type**: Integration Test
- **Scope**: Advanced

---

### Domain 7: Organizer Event Creation & Sales Tracking

#### AC-017: Create & Publish New Event Listing
- **Criteria ID**: AC-017
- **Title**: Create & Publish New Event Listing
- **Related Requirements**: US-015, FR-018, FR-019, PR-018, BR-010, UC-010
- **Given**: An authenticated Event Organizer is on the event creation form.
- **When**: The organizer inputs valid event details (Title: "Tech Summit", Date: Future Date, Venue: "Hall A", Capacity: 100, Price: \$50) and clicks "Publish Event".
- **Then**: The system shall save the Event and TicketTier database records with status `PUBLISHED`.
- **And**: The event shall render immediately in the public event catalog showcase.
- **Scenario Type**: Positive
- **Validation Type**: Integration / E2E Test
- **Scope**: MVP

---

### Domain 8: Accessibility & Security Behavioral Criteria

#### AC-018: Full Keyboard Booking Navigation (Accessibility)
- **Criteria ID**: AC-018
- **Title**: Full Keyboard Booking Navigation
- **Related Requirements**: US-003, FR-007, PR-026, BR-020, NFR-012
- **Given**: A user is navigating the public event catalog and checkout flow using only a keyboard.
- **When**: The user presses `Tab`, `Shift+Tab`, `Enter`, and `Space` keys.
- **Then**: All interactive elements (event cards, category buttons, quantity selectors, checkout inputs) shall receive visible focus indicators ($2\text{px}$ high-contrast ring).
- **And**: The user can complete event selection and checkout using keyboard controls exclusively.
- **Scenario Type**: Accessibility
- **Validation Type**: Accessibility / Manual Verification
- **Scope**: MVP

---

#### AC-019: Unauthorized Role Endpoint Rejection (Security)
- **Criteria ID**: AC-019
- **Title**: Unauthorized Role Endpoint Rejection
- **Related Requirements**: US-002, FR-004, PR-027, BR-016, NFR-009
- **Given**: A user is authenticated with the `ATTENDEE` role.
- **When**: The user submits an HTTP request to an organizer route (`/api/organizer/events`) or super-admin route (`/api/admin/suspend`).
- **Then**: The system shall block execution and return an HTTP 403 Forbidden response.
- **Scenario Type**: Security
- **Validation Type**: Security / API Test
- **Scope**: MVP

---

## 3. Definition of Done (DoD) — Sub-Phase 2.8
- [x] Acceptance Criteria written for key User Stories across all core domains using standard Given/When/Then format.
- [x] Positive, Negative, Boundary, Concurrency, Security, and Accessibility scenarios fully represented.
- [x] Critical Booking Core (AC-008, AC-009, AC-010), Payment Core (AC-011, AC-012, AC-013), Verification Core (AC-015, AC-016), and Auth/Security Core (AC-001, AC-003, AC-019) specified.
- [x] Saved to [`docs/requirements/acceptance-criteria.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/acceptance-criteria.md).
