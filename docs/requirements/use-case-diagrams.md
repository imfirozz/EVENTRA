# EVENTRA — Use Case Diagrams & Interaction Models

---

## 1. Customer Ticket Booking Use Case Diagram

```mermaid
flowchart LR
    Customer(("Customer (Attendee)"))
    PaymentGateway[["Payment Provider"]]
    EmailProvider[["Notification Provider"]]

    subgraph EVENTRA System Boundary
        UC003["UC-003: Discover & Search Events"]
        UC004["UC-004: View Event Details"]
        UC005["UC-005: Reserve Ticket Hold Inventory"]
        UC006["UC-006: Complete Checkout & Payment"]
        UC007["UC-007: Issue & Access Digital Ticket Pass"]
    end

    Customer --> UC003
    Customer --> UC004
    Customer --> UC005
    Customer --> UC006
    Customer --> UC007

    UC005 -.->|include| UC004
    UC006 -.->|include| UC005
    UC007 -.->|include| UC006

    UC006 <-->|Authorize Payment| PaymentGateway
    UC007 -->|Dispatch Email| EmailProvider
```

---

## 2. Gate Staff QR Verification Use Case Diagram

```mermaid
flowchart LR
    Staff(("Gate Inspector (Staff)"))
    Attendee(("Attendee (Customer)"))

    subgraph Gate Verification Boundary
        UC008["UC-008: Verify QR Pass at Venue Gate"]
    end

    Staff --> UC008
    Attendee -.->|Presents QR Pass| UC008
```

---

## 3. Organizer Event Lifecycle Use Case Diagram

```mermaid
flowchart LR
    Organizer(("Event Organizer"))

    subgraph Organizer Management Boundary
        UC010["UC-010: Create & Publish Event Listing"]
        UC011["UC-011: Monitor Sales Analytics"]
    end

    Organizer --> UC010
    Organizer --> UC011
```

---

## 4. Definition of Done (DoD) — Use Case Diagrams
- [x] Mermaid diagrams created for Customer Booking, Gate Verification, and Organizer Event Lifecycle.
- [x] Saved to [`docs/requirements/use-case-diagrams.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/use-case-diagrams.md).
