# EVENTRA — Functional Requirements Specification (FRS)

---

## 1. Overview & Formatting Rules

This document specifies the authoritative **Functional Requirements (FR-XXX)** for EVENTRA. Every requirement defines observable system behavior using a structured format specifying actor, trigger, preconditions, main flow, alternative flow, error flow, postconditions, and traceability metadata.

---

## 2. Functional Requirements Catalogue

---

### Domain 1: Account Registration

#### FR-001: Customer Account Registration
- **Name**: Customer Account Registration
- **Requirement**: WHEN an unregistered user submits valid registration details (Name, Email, Password), THE SYSTEM SHALL create an Attendee account, hash the password, initialize an account record, and return an authentication session.
- **Actor**: Unregistered User (Attendee)
- **Trigger**: User clicks "Register Account" button.
- **Preconditions**: User is on the Registration view and is not currently logged in.
- **Main Flow**:
  1. User inputs Name, Email, and Password.
  2. System validates email format and password strength (8+ chars).
  3. System verifies email is not already registered.
  4. System hashes password with salt.
  5. System creates Attendee account record.
  6. System creates active user session and returns success response.
- **Alternative Flow**: User registers via OAuth provider (Advanced Tier).
- **Exception/Error Flow**:
  - *Email Exists*: System returns error "Email address already registered".
  - *Invalid Data*: System highlights invalid fields with inline error messages.
- **Postconditions**: User account created; active session token issued.
- **Related Product Requirement**: PR-001
- **Related Business Requirement**: BR-009, BR-016
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: None
- **Validation Method**: Test (T)
- **Acceptance Condition**: Valid registration details create a new user record and issue an authenticated session token.

---

### Domain 2: Login

#### FR-002: User Authentication & Session Login
- **Name**: User Authentication & Session Login
- **Requirement**: WHEN a user inputs valid email and password credentials, THE SYSTEM SHALL verify credentials against stored password hashes, issue an authenticated session token, and grant role-based access.
- **Actor**: Registered User (Attendee, Organizer, Staff, Admin)
- **Trigger**: User clicks "Log In" button.
- **Preconditions**: User account exists.
- **Main Flow**:
  1. User enters Email and Password.
  2. System retrieves stored password hash for email.
  3. System verifies password hash against entered password.
  4. System issues session token containing user ID and role.
  5. System redirects user to appropriate dashboard or return URL.
- **Alternative Flow**: User checks "Remember Me" to extend session lifetime.
- **Exception/Error Flow**:
  - *Invalid Credentials*: System returns "Invalid email or password" error without specifying which field failed.
  - *Account Suspended*: System returns "Account suspended by administrator".
- **Postconditions**: Active user session created with assigned role guards.
- **Related Product Requirement**: PR-001, PR-027
- **Related Business Requirement**: BR-009, BR-016
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: FR-001
- **Validation Method**: Test (T)
- **Acceptance Condition**: Valid credentials return HTTP 200 with session token; invalid credentials return HTTP 401.

---

### Domain 3: Logout

#### FR-003: User Session Logout
- **Name**: User Session Logout
- **Requirement**: WHEN an authenticated user initiates a logout action, THE SYSTEM SHALL invalidate the active user session token and clear authentication state.
- **Actor**: Authenticated User
- **Trigger**: User clicks "Log Out" button.
- **Preconditions**: User possesses an active session.
- **Main Flow**:
  1. User clicks "Log Out".
  2. System invalidates session token server-side.
  3. System clears client session cookie.
  4. System redirects user to public home page.
- **Alternative Flow**: N/A
- **Exception/Error Flow**: Session already expired $\rightarrow$ system clears client cookie and redirects.
- **Postconditions**: Session invalidated; user state returned to unauthenticated.
- **Related Product Requirement**: PR-001
- **Related Business Requirement**: BR-009, BR-016
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: FR-002
- **Validation Method**: Test (T)
- **Acceptance Condition**: After logging out, protected route requests return HTTP 401 Unauthorized.

---

### Domain 4: Session & Account Management

#### FR-004: Protected Route Authorization & Role Guarding
- **Name**: Protected Route Authorization & Role Guarding
- **Requirement**: WHILE an HTTP request is made to a protected endpoint, THE SYSTEM SHALL verify session token validity and enforce role-based access control (RBAC).
- **Actor**: System / Authenticated User
- **Trigger**: HTTP request received on protected route.
- **Preconditions**: Request contains session header/cookie.
- **Main Flow**:
  1. System extracts session token from request.
  2. System verifies token signature and expiration.
  3. System inspects user role (`ATTENDEE`, `ORGANIZER`, `GATE_STAFF`, `SUPER_ADMIN`).
  4. System verifies user role possesses required route permission.
  5. System allows request progression to domain handler.
- **Alternative Flow**: N/A
- **Exception/Error Flow**:
  - *No Token*: System returns 401 Unauthorized.
  - *Insufficient Role*: System returns 403 Forbidden.
- **Postconditions**: Authorized request executed; unauthorized request blocked.
- **Related Product Requirement**: PR-027
- **Related Business Requirement**: BR-016
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: FR-002
- **Validation Method**: Test (T)
- **Acceptance Condition**: Request without valid token returns 401; attendee attempting to access `/admin` returns 403.

---

### Domain 5: Password Recovery

#### FR-005: Self-Service Password Reset Request
- **Name**: Self-Service Password Reset Request
- **Requirement**: WHEN a user requests a password reset for a registered email, THE SYSTEM SHALL generate a short-lived password reset token (15-min TTL) and dispatch a reset link to the email.
- **Actor**: Registered User
- **Trigger**: User submits email on "Forgot Password" view.
- **Preconditions**: User is on password recovery view.
- **Main Flow**:
  1. User enters account email.
  2. System verifies email existence.
  3. System generates time-limited reset token.
  4. System dispatches password reset email containing token URL.
  5. System presents confirmation message to user.
- **Alternative Flow**: N/A
- **Exception/Error Flow**: Email not registered $\rightarrow$ system displays generic confirmation message without revealing email non-existence (prevents email enumeration).
- **Postconditions**: Reset token generated; recovery email dispatched.
- **Related Product Requirement**: PR-001
- **Related Business Requirement**: BR-009, BR-016
- **Priority**: Should | **Scope**: Advanced
- **Dependencies**: FR-001
- **Validation Method**: Test (T)
- **Acceptance Condition**: Reset link opens password change view; token expires after 15 minutes.

---

### Domain 6: User Profile

#### FR-006: User Profile Management
- **Name**: User Profile Management
- **Requirement**: WHEN an authenticated user submits updated profile information (Name, Phone), THE SYSTEM SHALL update user profile records and return the updated profile.
- **Actor**: Authenticated User
- **Trigger**: User submits profile edit form.
- **Preconditions**: User is authenticated.
- **Main Flow**:
  1. User modifies profile fields and clicks "Save Profile".
  2. System validates input data syntax.
  3. System updates user profile record in database.
  4. System returns updated profile data.
- **Alternative Flow**: N/A
- **Exception/Error Flow**: Invalid input syntax $\rightarrow$ system returns 400 Validation Error with specific field messages.
- **Postconditions**: Profile updated in persistence storage.
- **Related Product Requirement**: PR-001
- **Related Business Requirement**: BR-009
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: FR-002
- **Validation Method**: Test (T)
- **Acceptance Condition**: Modified profile details persist across logins.

---

### Domain 7: Event Discovery

#### FR-007: Public Event Catalog Rendering
- **Name**: Public Event Catalog Rendering
- **Requirement**: WHEN a user requests the public event catalog, THE SYSTEM SHALL query active published events and return a list of event summary objects.
- **Actor**: Anonymous Visitor / Customer
- **Trigger**: User loads homepage or catalog route.
- **Preconditions**: System contains published events.
- **Main Flow**:
  1. User navigates to catalog view.
  2. System queries active published events (where `status = PUBLISHED` and `end_date > NOW()`).
  3. System formats event summary data (Title, Date, Venue, Starting Price, Banner Image URL).
  4. System renders event card list.
- **Alternative Flow**: N/A
- **Exception/Error Flow**: Zero active events $\rightarrow$ system renders empty catalog view with friendly prompt message.
- **Postconditions**: Event list displayed to user.
- **Related Product Requirement**: PR-002
- **Related Business Requirement**: BR-001
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: None
- **Validation Method**: Demonstration (D)
- **Acceptance Condition**: Active published events render on homepage matching responsive viewport rules.

---

### Domain 8: Event Details

#### FR-008: Event Detail Metadata Retrieval
- **Name**: Event Detail Metadata Retrieval
- **Requirement**: WHEN a user selects a specific event ID, THE SYSTEM SHALL retrieve and display comprehensive event details, including schedule, venue address, organizer info, and ticket tier availability.
- **Actor**: Anonymous Visitor / Customer
- **Trigger**: User clicks event card.
- **Preconditions**: Target event exists and is published.
- **Main Flow**:
  1. User selects event card.
  2. System fetches event record, venue details, organizer profile, and ticket tiers.
  3. System calculates remaining stock for each ticket tier.
  4. System renders event detail view.
- **Alternative Flow**: N/A
- **Exception/Error Flow**:
  - *Event Not Found*: System returns 404 Not Found page.
  - *Event Draft/Unpublished*: System returns 403 Forbidden (unless requesting user is the event organizer).
- **Postconditions**: Event detail view rendered.
- **Related Product Requirement**: PR-003
- **Related Business Requirement**: BR-002
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: FR-007
- **Validation Method**: Test (T) & Demonstration (D)
- **Acceptance Condition**: Event page displays accurate event metadata and active ticket selection options.

---

### Domain 9: Event Search

#### FR-009: Catalog Keyword Search Processing
- **Name**: Catalog Keyword Search Processing
- **Requirement**: WHEN a user enters a search text string, THE SYSTEM SHALL filter the catalog to return events matching the search string in title, category, or description.
- **Actor**: Customer / Visitor
- **Trigger**: User submits text query in catalog search bar.
- **Preconditions**: Catalog view active.
- **Main Flow**:
  1. User types query string (e.g. "React") and submits.
  2. System executes case-insensitive match against event titles, categories, and descriptions.
  3. System updates catalog view with matching event results.
- **Alternative Flow**: User clears search query $\rightarrow$ system restores full catalog list.
- **Exception/Error Flow**: No matches found $\rightarrow$ system displays "No matching events found" message with "Clear Filters" button.
- **Postconditions**: Filtered list rendered.
- **Related Product Requirement**: PR-004
- **Related Business Requirement**: BR-015
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: FR-007
- **Validation Method**: Test (T)
- **Acceptance Condition**: Entering "Tech" displays only events containing "Tech" in metadata.

---

### Domain 10: Event Filtering

#### FR-010: Category-Based Catalog Filtering
- **Name**: Category-Based Catalog Filtering
- **Requirement**: WHEN a user selects an event category filter tag (e.g. Workshop, Meetup, Conference), THE SYSTEM SHALL filter the active catalog list to show only events assigned to that category.
- **Actor**: Customer / Visitor
- **Trigger**: User clicks category filter button.
- **Preconditions**: Catalog view active.
- **Main Flow**:
  1. User clicks category tag (e.g. "Workshop").
  2. System filters event dataset where `category = 'Workshop'`.
  3. System renders filtered event card list.
- **Alternative Flow**: User selects "All Categories" $\rightarrow$ system displays full catalog.
- **Exception/Error Flow**: N/A
- **Postconditions**: Filtered event cards rendered.
- **Related Product Requirement**: PR-004
- **Related Business Requirement**: BR-015
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: FR-007
- **Validation Method**: Demonstration (D)
- **Acceptance Condition**: Selecting "Conference" hides non-conference event cards.

---

### Domain 11: Inventory Availability & Real-Time Stock

#### FR-011: Ticket Stock Availability Verification
- **Name**: Ticket Stock Availability Verification
- **Requirement**: WHEN a user selects a ticket tier and quantity, THE SYSTEM SHALL verify that `AvailableStock >= RequestedQuantity` (where $AvailableStock = TotalCapacity - SoldTickets - ActiveReservedHolds$).
- **Actor**: Customer / System
- **Trigger**: User selects ticket quantity on event page.
- **Preconditions**: Event page loaded.
- **Main Flow**:
  1. User selects ticket quantity (e.g. 2 General Admission tickets).
  2. System queries current sold count and active non-expired hold reservations.
  3. System confirms requested quantity is available.
  4. System enables "Reserve Tickets" checkout button.
- **Alternative Flow**: Available stock $<6$ $\rightarrow$ System displays "Only X tickets left!" warning badge.
- **Exception/Error Flow**: Stock $=0$ $\rightarrow$ System displays "Sold Out" badge and disables selection controls.
- **Postconditions**: Stock status verified and UI controls updated.
- **Related Product Requirement**: PR-006
- **Related Business Requirement**: BR-004
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: FR-008
- **Validation Method**: Test (T)
- **Acceptance Condition**: System accurately calculates remaining available stock and blocks selection when stock is exhausted.

---

### Domain 12: Temporary Ticket Reservation Hold (Critical Booking Core)

#### FR-012: Atomic Ticket Reservation Hold Lock
- **Name**: Atomic Ticket Reservation Hold Lock
- **Requirement**: WHEN a user initiates a ticket reservation for a valid quantity, THE SYSTEM SHALL atomically reserve the tickets for 300 seconds (5 minutes), decrement available stock count, create a reservation hold record in `RESERVED` state, and return a 300-second countdown timer.
- **Actor**: Customer (Attendee)
- **Trigger**: User clicks "Reserve Tickets" button.
- **Preconditions**: User is logged in; requested stock is available.
- **Main Flow**:
  1. User clicks "Reserve Tickets".
  2. System initiates atomic reservation transaction.
  3. System re-verifies stock availability under row lock.
  4. System creates booking record in `RESERVED` state.
  5. System sets reservation expiration timestamp ($NOW + 300\text{s}$).
  6. System returns reservation token and 300-second timer to client.
- **Alternative Flow**: User completes payment before 300s $\rightarrow$ System converts reservation state to `PAID` (FR-014).
- **Exception/Error Flow**:
  - *Stock Exhausted during Click*: System aborts transaction, returns 409 Conflict error "Tickets no longer available".
  - *Reservation Timeout*: User takes $>300\text{s}$ $\rightarrow$ Expiration background task marks reservation `EXPIRED` and restores stock count (FR-013).
  - *Duplicate Click / Multiple Tabs*: System detects existing active reservation for user $\rightarrow$ prompts user to continue existing checkout or cancel it.
- **Postconditions**: Stock temporarily locked; 300s TTL timer running.
- **Related Product Requirement**: PR-007
- **Related Business Requirement**: BR-004, BR-005
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: FR-011
- **Validation Method**: Test (T)
- **Acceptance Condition**: Initiating reservation locks stock for 300s; concurrent requests for the last remaining ticket result in exactly 1 success and 1 409 Conflict error.

---

### Domain 13: Booking Reservation Expiration & Stock Release

#### FR-013: Automatic Reservation Expiration & Stock Recovery
- **Name**: Automatic Reservation Expiration & Stock Recovery
- **Requirement**: WHEN a reservation hold reaches its 300-second TTL expiration without payment completion, THE SYSTEM SHALL automatically transition the booking state from `RESERVED` to `EXPIRED` and restore the reserved inventory count back to public available stock.
- **Actor**: System (Expiration Worker)
- **Trigger**: Reservation timer exceeds 300 seconds without payment verification.
- **Preconditions**: Booking record exists in `RESERVED` state.
- **Main Flow**:
  1. Reservation TTL timer expires (or user cancels checkout).
  2. System executes state transition `RESERVED` $\rightarrow$ `EXPIRED`.
  3. System releases locked inventory count back to available stock.
  4. Client UI displays "Reservation Expired" alert and redirects user to catalog.
- **Alternative Flow**: User clicks "Cancel Reservation" on checkout page $\rightarrow$ System immediately releases hold.
- **Exception/Error Flow**: Client attempts to submit payment for expired reservation $\rightarrow$ System rejects checkout with error "Reservation expired. Please select tickets again."
- **Postconditions**: Booking state `EXPIRED`; stock restored to public availability.
- **Related Product Requirement**: PR-007
- **Related Business Requirement**: BR-005
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: FR-012
- **Validation Method**: Test (T)
- **Acceptance Condition**: Uncompleted reservations automatically expire after 300s and restore stock to public catalog.

---

### Domain 14: Payment Processing & Order Fulfillment (Critical Payment Core)

#### FR-014: Payment Processing & Reconciled Order Fulfillment
- **Name**: Payment Processing & Reconciled Order Fulfillment
- **Requirement**: WHEN a valid payment confirmation is verified for an active `RESERVED` booking, THE SYSTEM SHALL atomically transition the booking state from `RESERVED` to `PAID`, generate digital QR ticket pass records, and dispatch a confirmation email.
- **Actor**: Customer / System (Payment Webhook)
- **Trigger**: Payment authorization succeeds (simulated sandbox for MVP; verified gateway webhook for Advanced).
- **Preconditions**: Booking exists in `RESERVED` state and reservation timer is active ($<300\text{s}$).
- **Main Flow**:
  1. Customer submits payment credentials.
  2. System processes payment authorization (or receives Stripe webhook).
  3. System verifies payment authorization success and payload transaction ID.
  4. System atomically updates booking state: `RESERVED` $\rightarrow$ `PAID`.
  5. System generates unique digital ticket record(s) linked to order.
  6. System triggers asynchronous confirmation email job.
  7. System returns order confirmation view to customer.
- **Alternative Flow**: Payment gateway delayed callback $\rightarrow$ Webhook processes state update asynchronously; user views updated status upon poll/refresh.
- **Exception/Error Flow**:
  - *Payment Declined*: System returns payment error message; booking remains `RESERVED` within remaining timer window.
  - *Duplicate Webhook Delivery*: System verifies idempotency key $\rightarrow$ ignores duplicate webhook payload without creating duplicate tickets.
  - *Payment Arrives After Expiration*: System detects payment received for `EXPIRED` booking $\rightarrow$ marks order `PAYMENT_MISMATCH_REFUND_PENDING` and triggers automatic refund workflow.
- **Postconditions**: Booking state `PAID`; tickets generated; email queued.
- **Related Product Requirement**: PR-009, PR-010
- **Related Business Requirement**: BR-006, BR-014, BR-019
- **Priority**: Must | **Scope**: MVP (Sandbox) / Advanced (Live Webhook)
- **Dependencies**: FR-012
- **Validation Method**: Test (T)
- **Acceptance Condition**: Verified payment transitions booking to `PAID` and creates ticket records; duplicate webhooks handle idempotently.

---

### Domain 15: Digital Ticket Generation & Verification (Critical Ticket Core)

#### FR-015: Digital Ticket Generation & HMAC QR Signing
- **Name**: Digital Ticket Generation & HMAC QR Signing
- **Requirement**: WHEN a booking transitions to `PAID`, THE SYSTEM SHALL generate a digital ticket record containing ticket ID, event ID, attendee name, seat tier, and a cryptographically signed HMAC-SHA256 QR payload string.
- **Actor**: System
- **Trigger**: Booking state changes to `PAID`.
- **Preconditions**: Order payment confirmed.
- **Main Flow**:
  1. System creates Ticket entity record for each ticket in order.
  2. System assigns initial ticket status = `PAID`.
  3. System constructs QR payload string (`ticket_id:event_id:timestamp`).
  4. System computes HMAC-SHA256 signature using server secret key and event salt.
  5. System appends signature to payload and renders QR image data URL.
  6. System links digital pass to attendee account order history.
- **Alternative Flow**: N/A
- **Exception/Error Flow**: System QR generation error $\rightarrow$ logs error to recovery queue and notifies admin.
- **Postconditions**: Signed digital ticket pass created and viewable by attendee.
- **Related Product Requirement**: PR-011
- **Related Business Requirement**: BR-007, BR-016
- **Priority**: Must (Basic QR) / Should (HMAC Signature) | **Scope**: MVP / Advanced
- **Dependencies**: FR-014
- **Validation Method**: Test (T)
- **Acceptance Condition**: Paid bookings generate digital tickets with QR payloads containing valid HMAC signatures.

---

#### FR-016: Gate Scanner QR Entry Verification
- **Name**: Gate Scanner QR Entry Verification
- **Requirement**: WHEN an authorized gate inspector scans an attendee QR code, THE SYSTEM SHALL verify payload HMAC signature integrity and ticket status, and if state = `PAID`, atomically transition status to `CHECKED_IN` and return a visual Green "VALID" feedback response.
- **Actor**: Gate Inspector (Staff)
- **Trigger**: Staff camera scans QR pass at venue door.
- **Preconditions**: Staff is authenticated with `GATE_STAFF` role; ticket exists.
- **Main Flow**:
  1. Staff scans attendee QR pass.
  2. System verifies HMAC signature against server secret key.
  3. System queries ticket entity by ticket ID.
  4. System verifies current ticket status equals `PAID`.
  5. System atomically updates ticket status: `PAID` $\rightarrow$ `CHECKED_IN`.
  6. System logs check-in event (timestamp, gate ID, staff user ID).
  7. System displays Green "VALID — Check-In Complete" full-screen feedback.
- **Alternative Flow**: N/A
- **Exception/Error Flow**:
  - *Already Checked In*: Ticket status = `CHECKED_IN` $\rightarrow$ System displays Red "ALREADY USED" screen showing original check-in timestamp.
  - *Invalid/Tampered QR*: HMAC signature check fails $\rightarrow$ System displays Red "INVALID TICKET PASS" screen.
  - *Ticket Cancelled*: Ticket status = `CANCELLED` $\rightarrow$ System displays Red "CANCELLED TICKET" screen.
- **Postconditions**: Ticket state updated to `CHECKED_IN`; entry audit log written.
- **Related Product Requirement**: PR-012, PR-022
- **Related Business Requirement**: BR-008, BR-012
- **Priority**: Should | **Scope**: Advanced
- **Dependencies**: FR-015
- **Validation Method**: Test (T) & Demonstration (D)
- **Acceptance Condition**: Valid QR scan updates ticket state to `CHECKED_IN` and returns Green response; rescanning same QR returns Red "ALREADY USED".

---

### Domain 16: Customer Booking History

#### FR-017: Customer Order History Portal
- **Name**: Customer Order History Portal
- **Requirement**: WHEN a logged-in attendee requests their order history, THE SYSTEM SHALL query all bookings associated with the user ID and return a list of active and past orders with viewable digital tickets.
- **Actor**: Authenticated Customer (Attendee)
- **Trigger**: User navigates to "My Tickets" or "Order History" view.
- **Preconditions**: User is authenticated.
- **Main Flow**:
  1. User opens "My Tickets" view.
  2. System fetches all orders matching `user_id = CurrentUserId`.
  3. System renders order list showing event title, date, ticket tier, order status (`PAID`, `EXPIRED`, `CANCELLED`), and "View Ticket" button.
  4. User clicks "View Ticket" $\rightarrow$ System displays digital QR pass.
- **Alternative Flow**: N/A
- **Exception/Error Flow**: User has no bookings $\rightarrow$ System displays "No ticket purchases yet" with button to explore event catalog.
- **Postconditions**: Order history rendered.
- **Related Product Requirement**: PR-013
- **Related Business Requirement**: BR-009
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: FR-002, FR-014
- **Validation Method**: Test (T) & Demonstration (D)
- **Acceptance Condition**: User can view all past orders and display digital ticket passes for active paid bookings.

---

### Domain 17: Organizer Event Creation & Publishing

#### FR-018: Organizer Event Creation Wizard
- **Name**: Organizer Event Creation Wizard
- **Requirement**: WHEN an authenticated organizer submits valid event parameters (Title, Category, Description, Date/Time, Venue, Capacity, Pricing), THE SYSTEM SHALL create an Event record and link configured ticket tiers.
- **Actor**: Authenticated Organizer
- **Trigger**: Organizer submits "Create Event" form.
- **Preconditions**: User possesses `ORGANIZER` role.
- **Main Flow**:
  1. Organizer inputs event details and ticket tier settings.
  2. System validates that event date is in the future and total capacity is $>0$.
  3. System creates Event record in `DRAFT` state.
  4. System creates associated TicketTier records linked to event.
  5. System returns success message with option to preview or publish.
- **Alternative Flow**: Organizer saves as `DRAFT` $\rightarrow$ Event saved without publishing to public catalog.
- **Exception/Error Flow**: Past event date or zero capacity $\rightarrow$ System returns validation error highlighting invalid fields.
- **Postconditions**: Event entity created in database.
- **Related Product Requirement**: PR-018, PR-019
- **Related Business Requirement**: BR-010
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: FR-004
- **Validation Method**: Test (T) & Demonstration (D)
- **Acceptance Condition**: Valid event form creates Event and TicketTier database records linked to the organizer account.

---

#### FR-019: Event Publication to Catalog
- **Name**: Event Publication to Catalog
- **Requirement**: WHEN an organizer publishes a draft event, THE SYSTEM SHALL transition the event state from `DRAFT` to `PUBLISHED` and make the event discoverable in the public event catalog.
- **Actor**: Authenticated Organizer
- **Trigger**: Organizer clicks "Publish Event" button.
- **Preconditions**: Event exists in `DRAFT` state and belongs to organizer.
- **Main Flow**:
  1. Organizer clicks "Publish Event".
  2. System verifies event metadata completeness.
  3. System updates event status: `DRAFT` $\rightarrow$ `PUBLISHED`.
  4. System invalidates public catalog cache.
  5. System returns live public URL to organizer.
- **Alternative Flow**: N/A
- **Exception/Error Flow**: Incomplete event details (missing title or pricing) $\rightarrow$ System blocks publication and highlights missing requirements.
- **Postconditions**: Event status `PUBLISHED`; event visible on public catalog.
- **Related Product Requirement**: PR-018
- **Related Business Requirement**: BR-010
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: FR-018
- **Validation Method**: Test (T)
- **Acceptance Condition**: Publishing a draft event updates status to `PUBLISHED` and renders event on public landing page.

---

### Domain 18: Organizer Sales Dashboard

#### FR-020: Organizer Real-Time Sales Metrics
- **Name**: Organizer Real-Time Sales Metrics
- **Requirement**: WHEN an organizer views their management dashboard, THE SYSTEM SHALL query total tickets sold, remaining available capacity, and total revenue for all events owned by the organizer.
- **Actor**: Authenticated Organizer
- **Trigger**: Organizer loads dashboard view.
- **Preconditions**: Organizer is authenticated.
- **Main Flow**:
  1. Organizer opens dashboard.
  2. System queries events where `organizer_id = CurrentUserId`.
  3. System calculates aggregate sales metrics per event ($SoldTickets$, $RemainingStock$, $GrossRevenue$).
  4. System renders dashboard event summary cards.
- **Alternative Flow**: N/A
- **Exception/Error Flow**: N/A
- **Postconditions**: Dashboard sales summary rendered.
- **Related Product Requirement**: PR-020, PR-021
- **Related Business Requirement**: BR-011
- **Priority**: Must (Sales Count MVP) / Should (Revenue Advanced) | **Scope**: MVP / Advanced
- **Dependencies**: FR-018
- **Validation Method**: Test (T)
- **Acceptance Condition**: Dashboard accurately calculates and displays tickets sold and revenue figures for organizer events.

---

### Domain 19: Ticket Transfer (Pending Product Decision / Out of Scope)

#### FR-021: Peer-to-Peer Ticket Transfer (OUT OF SCOPE)
- **Name**: Peer-to-Peer Ticket Transfer (OUT OF SCOPE)
- **Requirement**: Pending Product Decision — THE SYSTEM SHALL NOT support peer-to-peer ticket reselling, scalping marketplace exchanges, or secondary ticket ownership transfers for initial releases.
- **Actor**: N/A
- **Trigger**: N/A
- **Preconditions**: Explicitly out of scope boundary per Phase 1 Scope Freeze.
- **Main Flow**: N/A
- **Alternative Flow**: N/A
- **Exception/Error Flow**: N/A
- **Postconditions**: Feature disabled / omitted.
- **Related Product Requirement**: PR-015
- **Related Business Requirement**: BR-007 (Boundary)
- **Priority**: Out of Scope | **Scope**: Out of Scope
- **Dependencies**: None
- **Validation Method**: Inspection (I)
- **Acceptance Condition**: Platform contains zero secondary ticket transfer or resale features.

---

### Domain 20: Notifications

#### FR-022: Asynchronous Transactional Email Confirmation Dispatch
- **Name**: Asynchronous Transactional Email Confirmation Dispatch
- **Requirement**: WHEN a booking transitions to `PAID`, THE SYSTEM SHALL enqueue an asynchronous background email task to format and dispatch an order confirmation email to the attendee address.
- **Actor**: System (Background Email Queue)
- **Trigger**: Booking state changes to `PAID`.
- **Preconditions**: Order confirmed and attendee email available.
- **Main Flow**:
  1. System receives `BookingPaidEvent`.
  2. System formats HTML confirmation template with Event Title, Date, Order ID, Ticket Tier, and Digital Pass link.
  3. System enqueues job into background worker queue.
  4. Background worker dispatches email via SMTP adapter.
  5. System records email delivery status log.
- **Alternative Flow**: N/A
- **Exception/Error Flow**: SMTP delivery failure $\rightarrow$ Background worker retries job up to 3 times with exponential backoff before logging failure to admin queue.
- **Postconditions**: Email dispatched to recipient inbox.
- **Related Product Requirement**: PR-016
- **Related Business Requirement**: BR-014
- **Priority**: Must | **Scope**: MVP
- **Dependencies**: FR-014
- **Validation Method**: Test (T)
- **Acceptance Condition**: Completed paid booking triggers asynchronous confirmation email delivery.

---

### Domain 21: Support Order Lookup & Refund Handling

#### FR-023: Customer Support Order Lookup & Ticket Cancellation
- **Name**: Customer Support Order Lookup & Ticket Cancellation
- **Requirement**: WHEN an authorized support operator searches an order ID or attendee email, THE SYSTEM SHALL return full order state history and provide an action to cancel un-scanned tickets and restore inventory.
- **Actor**: Support Operator / Admin
- **Trigger**: Support operator submits search or clicks "Cancel & Refund Ticket".
- **Preconditions**: Operator possesses administrative support privileges.
- **Main Flow**:
  1. Operator enters order ID or attendee email.
  2. System displays order metadata, payment status, and ticket states.
  3. Operator selects ticket and clicks "Cancel Ticket".
  4. System verifies ticket status equals `PAID` (and NOT `CHECKED_IN`).
  5. System updates ticket status: `PAID` $\rightarrow$ `CANCELLED`.
  6. System restores ticket inventory count back to available stock pool.
  7. System logs support audit trail entry.
- **Alternative Flow**: N/A
- **Exception/Error Flow**: Attempting to cancel already `CHECKED_IN` ticket $\rightarrow$ System blocks cancellation with error "Cannot cancel ticket that has already been checked in at venue door."
- **Postconditions**: Ticket state `CANCELLED`; inventory restored; audit log written.
- **Related Product Requirement**: PR-025
- **Related Business Requirement**: BR-017
- **Priority**: Should | **Scope**: Advanced
- **Dependencies**: FR-014
- **Validation Method**: Test (T)
- **Acceptance Condition**: Support can cancel un-scanned paid tickets, restoring stock count to available pool.

---

### Domain 22: Platform Administration & Audit Trail

#### FR-024: Super-Admin Event Moderation & Audit Logging
- **Name**: Super-Admin Event Moderation & Audit Logging
- **Requirement**: WHEN a super-admin suspends a flagged event listing, THE SYSTEM SHALL update event status to `SUSPENDED`, remove the event from public catalog views, and write an immutable audit log record.
- **Actor**: Super Admin
- **Trigger**: Super-admin submits event suspension action.
- **Preconditions**: User is authenticated with `SUPER_ADMIN` role.
- **Main Flow**:
  1. Super-admin inspects flagged event details.
  2. Admin enters administrative justification note and clicks "Suspend Event".
  3. System updates event status: `PUBLISHED` $\rightarrow$ `SUSPENDED`.
  4. System removes event from public catalog listing.
  5. System appends audit record (`admin_id`, `event_id`, `action`, `justification`, `timestamp`) to append-only audit trail.
- **Alternative Flow**: Admin reinstates event $\rightarrow$ Status changes `SUSPENDED` $\rightarrow$ `PUBLISHED`.
- **Exception/Error Flow**: Non-admin user attempts moderation endpoint $\rightarrow$ System returns 403 Forbidden.
- **Postconditions**: Event suspended; public visibility removed; audit log written.
- **Related Product Requirement**: PR-023
- **Related Business Requirement**: BR-013
- **Priority**: Could | **Scope**: Production
- **Dependencies**: FR-004, FR-019
- **Validation Method**: Test (T) & Inspection (I)
- **Acceptance Condition**: Admin suspension hides event from catalog and writes an unalterable audit log entry.

---

## 3. Definition of Done (DoD) — Sub-Phase 2.4
- [x] Functional requirements defined across all required functional domains using precise EARS format.
- [x] Actor, Trigger, Preconditions, Main Flow, Alternative Flow, Error Flow, Postconditions, Metadata documented for every FR.
- [x] Critical Booking Core (FR-011, FR-012, FR-013), Payment Core (FR-014), and Ticket Core (FR-015, FR-016) fully specified.
- [x] Saved to [`docs/requirements/functional-requirements.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/functional-requirements.md).
