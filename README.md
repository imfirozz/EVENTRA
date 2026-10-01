# EVENTRA — Event Ticketing & Booking Platform

> **EVENTRA** is a resume-grade full-stack event ticketing platform engineered to handle high-concurrency ticket reservations with zero overselling, absolute payment-inventory consistency, and frictionless gate check-in.

---

## 🚀 Overview

Built as a **Modular Monolith**, EVENTRA provides:
- **Attendees**: Fast event discovery, transparent pricing, guaranteed 5-minute reservation hold timers, and instant HMAC-signed QR passes.
- **Organizers**: Surge-proof ticket inventory management, sales analytics, and gate entry validation tools.
- **Engineers**: An exemplary production-grade SDLC implementation demonstrating concurrency safety, state machine design, and system observability.

---

## 📌 Development Roadmap

- [x] **Phase 1: Initiation & Planning**
  - [x] Sub-Phase 1.1: Project Identification (`v0.1.0`)
  - [x] Sub-Phase 1.2: Problem & Opportunity Definition
  - [x] Sub-Phase 1.3: Product Vision & Principles
- [ ] **Phase 2: Product Requirements & UX Architecture**
- [ ] **Phase 3: Frontend v0.1 (Task 1 Responsive Landing Page)**
- [ ] **Phase 4: Backend & Database (Modular Monolith)**
- [ ] **Phase 5: Concurrency Engine & Payments**
- [ ] **Phase 6: QR Ticketing & Gate Validation**
- [ ] **Phase 7: Hardening, Testing & Deployment**

---

## 📚 Project Documentation

- [`docs/01_PROJECT_IDENTIFICATION.md`](docs/01_PROJECT_IDENTIFICATION.md): Formal project identity & semantic versioning baseline.
- [`docs/02_PROBLEM_AND_OPPORTUNITY.md`](docs/02_PROBLEM_AND_OPPORTUNITY.md): Detailed problem statement, stakeholder analysis, and technical challenges.
- [`docs/03_PRODUCT_VISION.md`](docs/03_PRODUCT_VISION.md): Complete product vision, mission, target personas, and guiding principles.

---

## 🛠 Tech Stack (Evolutionary)

- **v0.1**: HTML5, CSS3 (Flexbox & Grid), JavaScript, Responsive Media Queries.
- **Target Monolith Stack**: Node.js / TypeScript, Express/NestJS, PostgreSQL, Redis (TTL Holds), Docker.
