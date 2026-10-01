# EVENTRA — Detailed Use Cases Specification

---

## 1. Customer & Booking Core Use Cases

---

### UC-005: Reserve Ticket Hold Inventory (Critical Booking Core)

- **Use Case Name**: Reserve Ticket Hold Inventory
- **Primary Actor**: Customer (Alex the Attendee)
- **Secondary Actors**: System (Inventory Engine)
- **Goal**: Temporarily lock ticket inventory for 300 seconds (5 minutes) while entering checkout info.
- **Scope**: MVP
- **Priority**: Must
- **Related BR**: BR-004, BR-005 | **Related PR**: PR-006, PR-007 | **Related FR**: FR-011, FR-012 | **Related US**: US-006, US-007
- **Preconditions**: Customer is logged in; target event has available stock ($Stock_{available} \ge Quantity$).
- **Trigger**: Customer clicks "Reserve Tickets" button on Event Detail view.
- **Main Success Scenario**:
  1. Customer selects ticket category and quantity (e.g. 2 General Admission tickets) and clicks "Reserve Tickets".
  2. System initiates atomic reservation transaction.
  3. System re-verifies stock availability under row lock ($Stock_{available} \ge 2$).
  4. System creates booking record in `RESERVED` state.
  5. System sets 300-second reservation expiration timestamp ($NOW + 300\text{s}$).
  6. System returns reservation token and starts 300-second checkout countdown timer in UI.
- **Alternative Flows**:
  - *A1: Customer Completes Payment before 300s*: Execution branches to UC-006 (Complete Checkout & Payment).
  - *A2: Customer Cancels Checkout*: Customer clicks "Cancel Reservation" $\rightarrow$ System releases hold lock immediately and restores stock.
- **Exception Flows**:
  - *E1: Stock Exhausted During Click*: Remaining stock reaches 0 before transaction completes $\rightarrow$ System returns 409 Conflict error: "Tickets no longer available."
  - *E2: Hold Timer Expiration*: Timer reaches 300s without payment verification $\rightarrow$ System expiration task transitions booking state `RESERVED` $\rightarrow$ `EXPIRED` and restores stock to public catalog.
  - *E3: Active Reservation Exists*: Customer already has an active hold timer $\rightarrow$ System prompts customer to complete active order or cancel it.
- **Postconditions**: Ticket inventory temporarily locked; 300-second timer active.
- **Success State**: Booking state = `RESERVED`; stock decremented by reserved quantity.
- **Failure State**: Booking state = `EXPIRED` or transaction aborted; stock restored.
- **Business Rules**: RULE-001 (Inventory Non-Negativity), RULE-002 (300s Hold Duration), RULE-006 (Max 6 Tickets Per Order).
- **Data Involved**: User ID, Event ID, Ticket Tier ID, Quantity, Reservation Token, Expiration Timestamp.
- **Security Considerations**: Rate limit reservation requests per IP/user to prevent inventory hoarding bots.
- **Accessibility Considerations**: `aria-live="polite"` timer announcements for screen readers.
- **Dependencies**: UC-004 (View Event Details & Availability).
- **Acceptance Reference**: FR-012 Acceptance Condition.

---

### UC-006: Complete Checkout & Payment (Critical Payment Core)

- **Use Case Name**: Complete Checkout & Payment
- **Primary Actor**: Customer (Alex the Attendee)
- **Secondary Actors**: Payment Provider (Stripe Gateway), System (Order Fulfillment Engine), Notification Provider
- **Goal**: Authorize payment for a `RESERVED` booking and fulfill confirmed order.
- **Scope**: MVP (Sandbox) / Advanced (Live Gateway)
- **Priority**: Must
- **Related BR**: BR-006, BR-014, BR-016 | **Related PR**: PR-008, PR-009, PR-010 | **Related FR**: FR-014, FR-022 | **Related US**: US-009, US-010, US-014
- **Preconditions**: Booking exists in `RESERVED` state and hold timer is active ($<300\text{s}$).
- **Trigger**: Customer submits payment credentials on checkout form.
- **Main Success Scenario**:
  1. Customer inputs contact info and payment details, then clicks "Pay Now".
  2. System submits payment payload to payment gateway.
  3. Payment gateway returns successful authorization confirmation and transaction ID.
  4. System verifies payment authorization payload.
  5. System atomically updates booking state: `RESERVED` $\rightarrow$ `PAID`.
  6. System creates digital QR ticket entities linked to order (UC-007).
  7. System enqueues asynchronous confirmation email task.
  8. System redirects customer to Order Confirmation view.
- **Alternative Flows**:
  - *A1: Asynchronous Gateway Webhook*: Gateway processing delayed $\rightarrow$ Webhook callback updates booking state to `PAID` asynchronously.
- **Exception Flows**:
  - *E1: Card Declined*: Bank rejects transaction $\rightarrow$ System displays payment error message; booking remains `RESERVED` within remaining timer window for user retry.
  - *E2: Payment Received After Expiration*: Delayed payment callback arrives for `EXPIRED` booking $\rightarrow$ System marks order `PAYMENT_MISMATCH_REFUND_PENDING` and triggers automatic refund workflow.
  - *E3: Duplicate Webhook Payload*: Gateway sends duplicate webhook $\rightarrow$ System verifies idempotency key and ignores duplicate payload.
- **Postconditions**: Booking state = `PAID`; tickets generated; confirmation email dispatched.
- **Success State**: Order confirmed; digital pass rendered.
- **Failure State**: Order remains `RESERVED` or marked `PAYMENT_MISMATCH_REFUND_PENDING`.
- **Business Rules**: RULE-003 (Immutable Ticket State), RULE-007 (Zero Plain Card Storage), RULE-008 (Webhook Signature Validation), RULE-009 (Idempotent Webhooks).
- **Data Involved**: Booking ID, User Contact Info, Payment Transaction ID, Order Total.
- **Security Considerations**: Strict HTTPS TLS 1.3; PCI SAQ A zero local card storage.
- **Accessibility Considerations**: Clear visual and text error alerts for declined payments.
- **Dependencies**: UC-005 (Reserve Ticket Hold Inventory).
- **Acceptance Reference**: FR-014 Acceptance Condition.

---

## 2. Gate Staff & Verification Use Cases

---

### UC-008: Verify QR Pass at Venue Gate

- **Use Case Name**: Verify QR Pass at Venue Gate
- **Primary Actor**: Event Staff (Marcus the Gate Inspector)
- **Secondary Actors**: Customer (Attendee), System (Verification Engine)
- **Goal**: Scan attendee digital QR pass and validate single-entry authorization.
- **Scope**: Advanced
- **Priority**: Should
- **Related BR**: BR-008, BR-012 | **Related PR**: PR-012, PR-022 | **Related FR**: FR-016 | **Related US**: US-012
- **Preconditions**: Gate Inspector is authenticated with `GATE_STAFF` role; attendee presents QR pass.
- **Trigger**: Staff points mobile camera scanner at attendee QR code.
- **Main Success Scenario**:
  1. Staff camera scans attendee QR pass.
  2. System parses QR payload string (`ticket_id:event_id:timestamp:hmac_sig`).
  3. System verifies HMAC-SHA256 signature using server secret key and event salt.
  4. System queries ticket entity by ticket ID.
  5. System verifies ticket status = `PAID`.
  6. System atomically updates ticket status: `PAID` $\rightarrow$ `CHECKED_IN`.
  7. System logs check-in event (timestamp, gate ID, staff user ID).
  8. System renders full-screen Green "VALID — Check-In Complete" visual response with audio beep.
- **Alternative Flows**: N/A
- **Exception Flows**:
  - *E1: Ticket Already Scanned*: Ticket status = `CHECKED_IN` $\rightarrow$ System displays Red "ALREADY USED" screen showing original check-in timestamp.
  - *E2: HMAC Signature Invalid*: QR payload tampered $\rightarrow$ System displays Red "INVALID PASS" screen.
  - *E3: Ticket Cancelled*: Ticket status = `CANCELLED` $\rightarrow$ System displays Red "CANCELLED TICKET" screen.
- **Postconditions**: Ticket state = `CHECKED_IN`; entry audit log written.
- **Success State**: Green "VALID" feedback; attendee admitted.
- **Failure State**: Red "REJECTED" feedback; attendee directed to resolution desk.
- **Business Rules**: RULE-004 (Single-Entry Gate Scanner), RULE-005 (Cryptographic Ticket Signing).
- **Data Involved**: Ticket ID, Event ID, Staff ID, HMAC Signature, Scan Timestamp.
- **Security Considerations**: Prevent replay attacks by marking ticket `CHECKED_IN` immediately upon scan.
- **Accessibility Considerations**: Full-screen visual color changes combined with distinct audio pitch signals.
- **Dependencies**: UC-007 (Issue & Access Digital Ticket Pass).
- **Acceptance Reference**: FR-016 Acceptance Condition.

---

## 3. Organizer Use Cases

---

### UC-010: Create & Publish Event Listing

- **Use Case Name**: Create & Publish Event Listing
- **Primary Actor**: Event Organizer (Sarah the Host)
- **Secondary Actors**: System (Catalog Engine)
- **Goal**: Input event metadata, pricing tiers, and capacity bounds, and publish event to public catalog.
- **Scope**: MVP
- **Priority**: Must
- **Related BR**: BR-010 | **Related PR**: PR-018, PR-019 | **Related FR**: FR-018, FR-019 | **Related US**: US-015
- **Preconditions**: User is authenticated as an Organizer.
- **Trigger**: Organizer submits "Create Event" form.
- **Main Success Scenario**:
  1. Organizer enters Title, Description, Date/Time, Venue, Banner Image, and Ticket Tiers (Price & Capacity).
  2. System validates input fields (future date, capacity $>0$).
  3. System creates Event record and associated TicketTier records in `DRAFT` state.
  4. Organizer clicks "Publish Event".
  5. System updates event status: `DRAFT` $\rightarrow$ `PUBLISHED`.
  6. System invalidates public catalog cache and renders event on public showcase.
- **Alternative Flows**: Organizer saves as `DRAFT` $\rightarrow$ Event saved for later editing without public listing.
- **Exception Flows**:
  - *E1: Invalid Parameters*: Past date or capacity $=0$ $\rightarrow$ System displays validation errors highlighting invalid fields.
- **Postconditions**: Event status = `PUBLISHED`; discoverable on public catalog.
- **Success State**: Event published live.
- **Failure State**: Event remains in `DRAFT` or validation errors shown.
- **Business Rules**: RULE-010 (Organizer Resource Ownership), RULE-011 (Published Event Immutability Boundaries).
- **Data Involved**: Event Title, Description, Date, Venue, Banner URL, Ticket Tier Prices, Capacities.
- **Security Considerations**: Verify organizer session ownership before saving or publishing.
- **Accessibility Considerations**: Accessible form labels and error descriptions.
- **Dependencies**: UC-002 (Authenticate & Log In User).
- **Acceptance Reference**: FR-018, FR-019 Acceptance Condition.

---

## 4. Entity Lifecycle State Specifications

### Booking Lifecycle States
- `PENDING`: Initial state during ticket selection.
- `RESERVED`: Inventory locked under 300-second TTL hold timer.
- `PAID`: Payment authorized and verified; tickets issued.
- `EXPIRED`: Hold timer reached 300s without payment; stock restored.
- `CANCELLED`: User or support cancelled booking; stock restored.
- `PAYMENT_MISMATCH_REFUND_PENDING`: Payment received after hold expired; automatic refund pending.

### Payment Lifecycle States
- `PENDING`: Payment checkout initiated.
- `SUCCESS`: Payment authorized by payment gateway.
- `FAILED`: Payment declined by bank.
- `CANCELLED`: User exited payment gateway.
- `REFUNDED`: Authorized support refund executed.

### Ticket Lifecycle States
- `ISSUED`: Ticket generated following booking payment.
- `CHECKED_IN`: Verified at venue entrance by gate scanner.
- `CANCELLED`: Ticket invalidated due to order cancellation/refund.

---

## 5. Definition of Done (DoD) — Use Cases
- [x] Detailed Use Cases specified using standard 18-point format.
- [x] Critical Booking (UC-005), Payment (UC-006), Verification (UC-008), and Organizer (UC-010) use cases defined with main, alternative, and exception flows.
- [x] Booking, Payment, and Ticket entity lifecycle states codified.
- [x] Saved to [`docs/requirements/use-cases.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/use-cases.md).
