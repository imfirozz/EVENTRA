# EVENTRA — Accessibility (a11y) & Inclusivity Specifications

---

## 1. Accessibility Standards Alignment

EVENTRA public attendee discovery and booking interfaces enforce **WCAG 2.1 AA** compliance standards to ensure complete usability for attendees with visual, motor, auditory, or cognitive impairments.

---

## 2. Technical Accessibility Specifications

### A. Keyboard Navigation & Focus Management
- **Rule**: 100% of interactive elements (buttons, links, form inputs, modal controls) MUST be focusable and operable via standard keyboard controls (`Tab`, `Shift+Tab`, `Enter`, `Space`, `Escape`).
- **Focus Rings**: Highly visible custom focus outline rings ($2\text{px}$ high-contrast border) MUST be displayed on focused elements. Focus styling MUST NOT be disabled using `outline: none`.
- **Focus Trapping**: Modal dialogs (e.g. checkout view) MUST trap keyboard focus inside the active modal until closed.

### B. Semantic HTML5 & Screen Reader Compatibility
- **Rule**: Interfaces MUST use native semantic HTML5 tags (`<main>`, `<nav>`, `<header>`, `<footer>`, `<section>`, `<article>`, `<button>`) instead of generic `<div>` click handlers.
- **Form Association**: Every form input MUST be explicitly linked to a corresponding `<label for="inputId">`.
- **Live Region Announcements**: Real-time status changes (e.g. reservation hold countdown timer) MUST use `aria-live="polite"` so screen readers announce remaining hold time without interrupting user actions. Critical form errors MUST use `aria-live="assertive"`.

### C. Color Contrast & Visual Design
- **Text Contrast**: Normal text ($<18\text{pt}$) MUST maintain a minimum contrast ratio of **4.5:1** against background colors. Large text ($\ge 18\text{pt}$) MUST maintain a minimum contrast ratio of **3.0:1**.
- **Color Independence**: Color MUST NOT be used as the sole visual indicator of state. Form errors MUST combine red borders with text error labels and warning icons. Gate scanner check-in feedback MUST combine full-screen color state changes with distinct visual text badges (GREEN "VALID" / RED "ALREADY USED") and distinct audio tones.

---

## 3. Definition of Done (DoD) — Accessibility
- [x] Keyboard navigation, semantic HTML5, aria-live, and color contrast rules specified.
- [x] Target automated accessibility audit score $\ge 95/100$ on AXE / Lighthouse.
- [x] Saved to [`docs/requirements/accessibility-requirements.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/requirements/accessibility-requirements.md).
