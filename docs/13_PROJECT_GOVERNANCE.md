# EVENTRA — Solo-Developer Project Governance Framework

---

## 1. Git Strategy & Branching Model

> [!NOTE] Trunk-Based Development with Short-Lived Feature Branches
> `main` is the single production-ready source of truth. All work takes place on short-lived feature branches merged back into `main` via PRs after passing automated CI checks.

```
main (Production Ready)
  ├── feat/subphase-1.1-repo-setup  ──(PR / CI Pass)──> Merge to main
  ├── feat/v0.1-landing-page        ──(PR / CI Pass)──> Merge to main
  └── fix/qr-hmac-signature-validation ──(PR / CI Pass)──> Merge to main
```

---

## 2. Branch Naming Conventions

Prefix format: `<type>/<short-kebab-case-description>`

- `feat/catalog-keyword-filter` (New feature addition)
- `fix/reservation-hold-timer-expiry` (Bug fix)
- `docs/subphase-1.13-governance` (Documentation addition/update)
- `refactor/booking-state-machine` (Code restructuring without behavior change)
- `chore/docker-compose-redis-upgrade` (Build/infra task)

---

## 3. Commit Conventions (Conventional Commits Standard)

Format: `<type>(<scope>): <imperative short summary>`

```bash
feat(booking): implement 5-minute atomic redis ttl hold lock
fix(qr): resolve HMAC-SHA256 signature verification offset
docs(adr): add ADR-001 modular monolith architecture decision
test(auth): add unit test coverage for argon2id password hashing
chore(deps): update express middleware dependencies
```

---

## 4. Pull Request (PR) Rules

Even for a solo developer, PRs provide a critical quality gate:
1. **Self-Code Review**: Inspect diff in GitHub / Git interface before merging.
2. **Automated CI Validation**: ESLint, TypeScript compilation, and Jest test suites must pass 100%.
3. **No Direct Pushes to `main`**: All commits reach `main` through PR merges to maintain clean history and auditability.

---

## 5. Issue Conventions & Label Taxonomy

### Issue Templates
- **Feature Issue**: Problem statement, proposed behavior, sub-phase mapping, Definition of Done.
- **Bug Issue**: Expected vs actual behavior, steps to reproduce, error stack trace.

### Minimal Label Taxonomy

| Category | Label Name | Purpose |
| :--- | :--- | :--- |
| **Type** | `type: feature` | New capabilities |
| | `type: bug` | Defect fixes |
| | `type: docs` | Documentation updates |
| | `type: refactor` | Code restructuring |
| **Phase** | `phase: 1-planning` | Phase 1 planning sub-phases |
| | `phase: 3-frontend` | Phase 3 UI baseline (Task 1 / v0.1) |
| | `phase: 4-backend` | Phase 4 Monolith backend & database |
| **Priority** | `priority: p0-blocker` | Critical path blocking items |
| | `priority: p1-high` | High value MVP items |
| | `priority: p2-medium` | Advanced tier items |

---

## 6. Milestones & Project Board

### Milestones Mapped to Semantic Releases
- 🏁 **Milestone 1**: `v0.1.0` — Responsive Landing Page UI Baseline (Task 1)
- 🏁 **Milestone 2**: `v0.5.0` — Modular Monolith Core Backend & Database Schema
- 🏁 **Milestone 3**: `v0.8.0` — Concurrency Engine, Stripe Webhooks & HMAC QR Passes
- 🏁 **Milestone 4**: `v1.0.0` — Hardened Production Deployment & Load Reports

### Kanban Project Board Columns
`Backlog` $\rightarrow$ `Ready for Dev` $\rightarrow$ `In Progress` $\rightarrow$ `In Review / CI` $\rightarrow$ `Done`

---

## 7. Definition of Ready (DoR) vs. Definition of Done (DoD)

### Definition of Ready (DoR)
A task is ready for development when:
1. Problem statement and user persona impact are clearly defined.
2. Feature scope is explicitly mapped to Tier A (MVP), Tier B, or Tier C in [`docs/05_SCOPE_DEFINITION.md`](05_SCOPE_DEFINITION.md).
3. Target sub-phase and acceptance criteria are specified.

### Definition of Done (DoD)
A task is complete when:
1. Code written according to project style guidelines.
2. Unit / Integration tests written with $\ge 80\%$ domain coverage.
3. Automated CI build and lint checks pass cleanly.
4. Relevant documentation updated (`README.md`, `docs/`, or `ADR`).
5. PR merged into `main` branch.

---

## 8. Change Management & ADR Workflow

- **Scope Change Gate**: Proposed feature changes must be evaluated against the Sub-Phase 1.5 Scope Freeze checklist before implementation.
- **Architectural Decision Records (ADRs)**: Stored under `docs/adr/NNNN-title.md`.

### Standard ADR Template
```markdown
# ADR-NNNN: [Short Title]

## Context
[What problem or architectural decision are we addressing?]

## Decision
[What is the chosen approach?]

## Rationale
[Why did we choose this over alternatives?]

## Consequences
[What are the trade-offs, risks, or follow-up requirements?]
```

---

## 9. Semantic Versioning & Release Tagging

Format: `v<MAJOR>.<MINOR>.<PATCH>`

- **`v0.1.0`**: Public Responsive Landing Page UI Baseline (Task 1).
- **`v0.5.0`**: Modular Monolith Core API (Auth, Catalog, Basic Booking DB Schema).
- **`v0.8.0`**: Advanced Concurrency Engine, Stripe Webhooks & Signed HMAC QR Passes.
- **`v1.0.0`**: Production Hardened System (Observability, Load Testing, Docker VPS Ready).

---

## 10. Definition of Done (DoD) — Sub-Phase 1.13
- [x] Lightweight solo-developer governance rules defined across all 14 required topics.
- [x] Conventional Commits, Git branching strategy, and PR rules established.
- [x] DoR, DoD, ADR template, and Semantic Versioning roadmap locked in.
- [x] Saved to [`docs/13_PROJECT_GOVERNANCE.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/13_PROJECT_GOVERNANCE.md).
