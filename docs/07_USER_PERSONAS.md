# EVENTRA — User Personas & Workflows

---

## 1. Persona 1: Customer (Attendee)

### Profile: "Alex the Tech Enthusiast"
- **Role**: Software Developer & Tech Conference Attendee
- **Technical Comfort**: High (Comfortable with modern web apps, mobile payments, digital QR tickets).
- **Typical Behavior**: Browses events on mobile/desktop, books tickets during flash-sale launch announcements, expects instantaneous digital delivery.

### Persona Attributes

| Dimension | Specification |
| :--- | :--- |
| **Goals** | Quick event discovery, guaranteed ticket reservation during high-demand sales, transparent checkout pricing, instant mobile QR pass access. |
| **Needs** | Real-time feedback on ticket hold timer, mobile-optimized event pages, offline access to ticket pass. |
| **Pain Points** | Losing a reserved ticket during payment processing due to overselling; hidden fees added at final step; unformatted email attachments. |
| **Important Workflows** | 1. Browse Catalog $\rightarrow$ 2. Select Ticket Tier $\rightarrow$ 3. Reserve Ticket (5-min Hold) $\rightarrow$ 4. Complete Checkout $\rightarrow$ 5. Receive & View QR Pass. |
| **Security Concerns** | Card details handling privacy, protection against spam email, assurance that ticket pass cannot be duplicated by unauthorized users. |
| **Accessibility Considerations** | Keyboard navigation throughout catalog/checkout, high contrast text ratios, screen-reader readable aria-live announcements for hold timer. |

---

## 2. Persona 2: Event Organizer

### Profile: "Sarah the Event Host"
- **Role**: Tech Community Lead & Conference Director
- **Technical Comfort**: Moderate-High (Uses cloud dashboards, spreadsheet data exports, social media campaigns).
- **Typical Behavior**: Creates events 1-3 months in advance, configures tiered ticket pricing, monitors sales velocity daily, exports attendee lists for badges.

### Persona Attributes

| Dimension | Specification |
| :--- | :--- |
| **Goals** | Sell out event capacity without overselling, track real-time revenue performance, easily check in attendees at venue doors. |
| **Needs** | Reliable inventory stock control, instant sales analytics, simple event setup workflow, exportable guest rosters (CSV). |
| **Pain Points** | Platform crashing during announcement emails; ticket overselling causing venue capacity violations; complex multi-step event setup forms. |
| **Important Workflows** | 1. Log into Dashboard $\rightarrow$ 2. Create Event & Set Capacity $\rightarrow$ 3. Configure Ticket Tiers & Prices $\rightarrow$ 4. Publish Event $\rightarrow$ 5. Monitor Live Sales & Revenue Analytics. |
| **Security Concerns** | Account takeover protection (MFA/strong pass), secure payout gateway configuration, authorization checks on organizer routes. |
| **Accessibility Considerations** | Form label clarity, screen-reader compatible data tables, keyboard navigable dashboard control controls. |

---

## 3. Persona 3: Event Staff (Gate Inspector)

### Profile: "Marcus the Gate Lead"
- **Role**: Venue Entry Inspector & Usher Lead
- **Technical Comfort**: Moderate (Uses smartphone apps daily, needs simple dead-shot interface).
- **Typical Behavior**: Stands at venue gate under high noise/crowd conditions, scans 500+ attendees in a 1-hour entry window, needs instant visual/audio check-in feedback.

### Persona Attributes

| Dimension | Specification |
| :--- | :--- |
| **Goals** | Move attendees through entry gates in $<3$ seconds per person with zero scanner errors. |
| **Needs** | Mobile web camera scanner app with immediate Green (Valid) / Red (Invalid/Already Used) visual indicator and audio beep. |
| **Pain Points** | Scanner freezing on bad network coverage; slow server validation response times; false positives on copied ticket screenshots. |
| **Important Workflows** | 1. Authenticate Scanner App with Gate Secret $\rightarrow$ 2. Point Camera at Attendee QR Code $\rightarrow$ 3. Receive Immediate Visual/Audio Validation $\rightarrow$ 4. Confirm Entry. |
| **Security Concerns** | Replay attack prevention (blocking second scan of same ticket), signature verification of HMAC-signed QR passes. |
| **Accessibility Considerations** | High contrast visual indicators (large full-screen color state changes), distinct audio pitch feedback (high beep for valid, low buzz for invalid), large tap targets. |

---

## 4. Persona 4: Platform Administrator

### Profile: "Devon the Platform Ops Lead"
- **Role**: System Reliability & Moderation Lead
- **Technical Comfort**: Expert (Database administrator, CLI power user, monitoring tool user).
- **Typical Behavior**: Monitors platform performance metrics, reviews flagged event listings, inspects audit logs during security or booking disputes.

### Persona Attributes

| Dimension | Specification |
| :--- | :--- |
| **Goals** | Maintain 99.9% platform uptime, eliminate fraudulent event listings, ensure compliance with security policies. |
| **Needs** | Comprehensive super-admin dashboard, audit trail logs, global user/organizer search, error metrics visualization. |
| **Pain Points** | Lack of visibility into unhandled background job errors; inability to trace failed payment webhooks; slow database query bottlenecks. |
| **Important Workflows** | 1. Review Platform Metrics $\rightarrow$ 2. Inspect Suspicious Organizer / Event Activity $\rightarrow$ 3. Moderate / Suspend Account $\rightarrow$ 4. Review Audit Log Trail. |
| **Security Concerns** | Immutable audit logs, strict RBAC enforcement, session timeout policy, principle of least privilege access. |
| **Accessibility Considerations** | Dense data table keyboard navigation, clean contrast ratios, structured heading hierarchies. |

---

## 5. Definition of Done (DoD) — Sub-Phase 1.7
- [x] Personas created for Customer, Event Organizer, Event Staff, and Platform Administrator.
- [x] All 10 persona criteria (Goals, Needs, Pain Points, Behaviors, Tech Comfort, Workflows, Security, Accessibility) documented without extraneous demographic fluff.
- [x] Documented and saved to [`docs/07_USER_PERSONAS.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/07_USER_PERSONAS.md).
