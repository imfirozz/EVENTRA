# EVENTRA — Repository & Project Management Setup

---

## 1. Initial Repository Directory Structure

```
Intership/
├── .github/                      <-- NOW: GitHub workflows, issue templates
│   ├── ISSUE_TEMPLATE/
│   │   ├── 01_feature_request.md
│   │   └── 02_bug_report.md
│   └── workflows/
│       └── ci.yml                <-- NOW: CI Workflow Placeholder
├── docs/                         <-- NOW: Comprehensive SDLC documentation
│   ├── 01_PROJECT_IDENTIFICATION.md
│   ├── 02_PROBLEM_AND_OPPORTUNITY.md
│   ├── 03_PRODUCT_VISION.md
│   ├── 04_GOALS_AND_SUCCESS_CRITERIA.md
│   ├── 05_SCOPE_DEFINITION.md
│   ├── 06_STAKEHOLDER_IDENTIFICATION.md
│   ├── 07_USER_PERSONAS.md
│   ├── 08_USER_JOURNEYS.md
│   ├── 09_ASSUMPTIONS_AND_CONSTRAINTS.md
│   ├── 10_FEASIBILITY_ANALYSIS.md
│   ├── 11_RISK_REGISTER.md
│   ├── 12_SECURITY_AND_PRIVACY_FOUNDATION.md
│   ├── 13_PROJECT_GOVERNANCE.md
│   ├── 14_DEVELOPMENT_METHODOLOGY.md
│   ├── 15_REPOSITORY_AND_PROJECT_MANAGEMENT.md
│   ├── 16_DOCUMENTATION_STRATEGY.md
│   ├── adr/
│   │   └── 0001-modular-monolith-architecture.md
│   └── security/
│       └── 01_SECURITY_FOUNDATION.md
├── src/                          <-- NOW: Source code directory
│   └── v0.1-landing/             <-- NOW: Task 1 Responsive HTML/CSS UI Baseline (Day_1)
│       ├── index.html
│       ├── css/
│       │   ├── variables.css
│       │   ├── styles.css
│       │   └── responsive.css
│       └── assets/
│           ├── images/
│           └── icons/
├── backend/                      <-- LATER: Phase 4 Modular Monolith API
│   ├── src/
│   │   ├── modules/ (catalog, booking, auth, payment, notification)
│   │   └── shared/
│   └── package.json
├── tests/                        <-- LATER: Integration & End-to-End Test Suite
│   ├── integration/
│   └── e2e/
├── infrastructure/               <-- LATER: Phase 7 Docker & Deployment Configs
│   ├── docker/
│   │   ├── docker-compose.yml
│   │   └── Dockerfile
│   └── Nginx/
├── scripts/                      <-- LATER: Seed scripts, migration helpers
│   ├── seed.sh
│   └── test-concurrency.js
├── .gitignore                    <-- NOW: Git ignore specification
└── README.md                     <-- NOW: Root Project Overview & Index
```

---

## 2. Directory Timeline: NOW vs LATER

| Path | Purpose | Required Timeline |
| :--- | :--- | :--- |
| `docs/` & `docs/adr/` | SDLC documentation & architecture decision records | **NOW (Phase 1)** |
| `.github/` | CI workflows & issue templates | **NOW (Phase 1)** |
| `src/v0.1-landing/` | Task 1 Responsive HTML5/CSS3 Landing Page UI Baseline | **NOW (Phase 1-3)** |
| `backend/` | Node.js/TypeScript Modular Monolith API | **LATER (Phase 4)** |
| `tests/` | Integration & E2E load test suites | **LATER (Phase 4-6)** |
| `infrastructure/` | Docker Compose, Nginx reverse proxy configs | **LATER (Phase 7)** |
| `scripts/` | Seed scripts & concurrency benchmark tools | **LATER (Phase 5-6)** |

---

## 3. GitHub Configuration Baseline

### A. `.gitignore` Rules
```gitignore
# Dependencies
node_modules/
.pnpm-store/

# Environment & Secrets
.env
.env.local
*.pem
*.key

# IDE & OS
.DS_Store
.vscode/
.idea/

# Build & Coverage Outputs
dist/
build/
coverage/
.next/

# Temporary Scratch Files
scratch/
*.log
```

### B. Branch Protection Rules (`main` Branch)
1. Require Pull Request before merging (no direct `git push` to `main`).
2. Require Status Checks (CI Workflow) to pass before merging.
3. Require conversation resolution before merging.

### C. GitHub Actions CI Placeholder (`.github/workflows/ci.yml`)
```yaml
name: EVENTRA Continuous Integration

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: HTML/CSS Syntax & Lint Validation Placeholder
        run: echo "CI Placeholder - Validation Passed for EVENTRA v0.1"
```

---

## 4. Definition of Done (DoD) — Sub-Phase 1.15
- [x] Initial repository structure defined with explicit NOW vs LATER demarcation.
- [x] `.gitignore`, GitHub CI placeholder, and branch protection rules specified.
- [x] Saved to [`docs/15_REPOSITORY_AND_PROJECT_MANAGEMENT.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/15_REPOSITORY_AND_PROJECT_MANAGEMENT.md).
