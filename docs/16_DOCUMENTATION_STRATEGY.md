# EVENTRA — Documentation Strategy & Information Architecture

---

## 1. Documentation Information Architecture

```
docs/
├── 01_PROJECT_IDENTIFICATION.md          <-- NOW (Phase 1)
├── 02_PROBLEM_AND_OPPORTUNITY.md        <-- NOW (Phase 1)
├── 03_PRODUCT_VISION.md                 <-- NOW (Phase 1)
├── 04_GOALS_AND_SUCCESS_CRITERIA.md     <-- NOW (Phase 1)
├── 05_SCOPE_DEFINITION.md               <-- NOW (Phase 1)
├── 06_STAKEHOLDER_IDENTIFICATION.md     <-- NOW (Phase 1)
├── 07_USER_PERSONAS.md                  <-- NOW (Phase 1)
├── 08_USER_JOURNEYS.md                  <-- NOW (Phase 1)
├── 09_ASSUMPTIONS_AND_CONSTRAINTS.md    <-- NOW (Phase 1)
├── 10_FEASIBILITY_ANALYSIS.md           <-- NOW (Phase 1)
├── 11_RISK_REGISTER.md                  <-- NOW (Phase 1)
├── 12_SECURITY_AND_PRIVACY_FOUNDATION.md <-- NOW (Phase 1)
├── 13_PROJECT_GOVERNANCE.md             <-- NOW (Phase 1)
├── 14_DEVELOPMENT_METHODOLOGY.md        <-- NOW (Phase 1)
├── 15_REPOSITORY_AND_PROJECT_MANAGEMENT.md <-- NOW (Phase 1)
├── 16_DOCUMENTATION_STRATEGY.md        <-- NOW (Phase 1)
├── requirements/                         <-- LATER (Phase 2)
│   ├── SRS_FUNCTIONAL.md
│   └── SRS_NON_FUNCTIONAL.md
├── architecture/                         <-- LATER (Phase 4)
│   ├── SYSTEM_ARCHITECTURE.md
│   ├── DOMAIN_BOUNDARIES.md
│   └── CONCURRENCY_ENGINE.md
├── adr/                                  <-- NOW & CONTINUOUS
│   └── 0001-modular-monolith-architecture.md
├── database/                             <-- LATER (Phase 4)
│   ├── SCHEMA_ERD.md
│   └── MIGRATION_GUIDE.md
├── api/                                  <-- LATER & AUTOMATED (Phase 4)
│   └── openapi.json / Swagger UI
├── security/                             <-- NOW & CONTINUOUS
│   └── 01_SECURITY_FOUNDATION.md
├── testing/                              <-- LATER (Phase 6)
│   └── TEST_PLAN_AND_REPORTS.md
├── deployment/                           <-- LATER (Phase 7)
│   └── DEPLOYMENT_GUIDE.md
├── runbooks/                             <-- LATER (Phase 7)
│   └── OPERATIONAL_RUNBOOK.md
├── CHANGELOG.md                          <-- AUTOMATED (On Releases)
└── README.md                             <-- NOW (Root Portal)
```

---

## 2. Documentation Classification Matrix

| Category | Documentation Artifact | Creation Stage | Maintenance Mode |
| :--- | :--- | :--- | :--- |
| **Project Planning** | Sub-Phases 1.1 – 1.16 Documents | **NOW** | Static baseline after Phase 1 lock |
| **Requirements** | Functional & Non-Functional SRS | **LATER (Phase 2)** | Updated during requirement changes |
| **Architecture** | System Architecture & Concurrency Engine | **LATER (Phase 4)** | Updated on major structural refactors |
| **ADRs** | Architecture Decision Records (`docs/adr/`) | **NOW & CONTINUOUS** | Append-only on architectural choices |
| **Database** | ERD Diagrams & Migration Logs | **LATER (Phase 4)** | Updated per DB schema migration |
| **API Docs** | OpenAPI (Swagger) Specification | **AUTOMATED (Phase 4)** | Generated automatically from code annotations |
| **Security** | Security Foundation & Threat Models | **NOW & CONTINUOUS** | Reviewed before every minor release |
| **Testing** | Test Plan & Load Performance Reports | **LATER (Phase 6)** | Generated automatically from test runs |
| **Deployment** | Docker & VPS Deployment Guide | **LATER (Phase 7)** | Updated on infra changes |
| **Runbooks** | Disaster Recovery & Operational Runbooks | **LATER (Phase 7)** | Updated on incident retrospectives |
| **Changelog** | `CHANGELOG.md` | **AUTOMATED** | Generated on semantic release tags |
| **Release Notes** | GitHub Release Summaries | **AUTOMATED** | Generated on `git tag` push |

---

## 3. Documentation Governance Rules

1. **Docs-as-Code**: All documentation resides directly in the Git repository in Markdown format (`.md`).
2. **Single Source of Truth**: Code comments serve inline documentation; `docs/` serves architectural and domain rationale.
3. **Automated Generation**: OpenAPI spec and test reports must be generated via npm scripts, not handwritten.

---

## 4. Definition of Done (DoD) — Sub-Phase 1.16
- [x] Complete documentation information architecture designed.
- [x] All 12 documentation categories classified across NOW, LATER, and AUTOMATED stages.
- [x] Saved to [`docs/16_DOCUMENTATION_STRATEGY.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/16_DOCUMENTATION_STRATEGY.md).
