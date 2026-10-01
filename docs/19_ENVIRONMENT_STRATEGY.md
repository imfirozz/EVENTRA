# EVENTRA — Environment Strategy & Management

---

## 1. Multi-Tier Environment Matrix

| Dimension | Local (`local`) | Development / CI (`ci`) | Staging (`staging`) | Production (`production`) |
| :--- | :--- | :--- | :--- | :--- |
| **Purpose** | Feature coding, UI iteration, unit testing. | Automated CI pipeline validation on GitHub Actions. | Pre-release verification & end-to-end integration testing. | Live platform serving attendees, organizers, & gate staff. |
| **Configuration** | Local Node.js + static web server + Docker Compose (Postgres/Redis). | Ephemeral GitHub Actions runner container environment. | Containerized Docker Compose on single cloud VPS instance. | Hardened Docker containers behind Nginx proxy with SSL. |
| **Data Policy** | Synthetic local seed scripts; zero real user data. | Isolated temporary test database destroyed after CI run. | Anonymized benchmark dataset matching production schema. | Real user/event data; encrypted at rest; daily offsite backups. |
| **Secrets** | `.env.local` containing mock developer keys (`secret_dev`). | GitHub Repository Secrets (`secrets.TEST_JWT_SECRET`). | Staging OS environment variables (`sk_test_...`). | Production environment variables (`sk_live_...`) injected at runtime. |
| **External Services** | Stripe Sandbox / Mock API; local Mailhog SMTP. | Mocked external HTTP calls (Jest / Nock). | Live Stripe Test Mode (`sk_test_...`); SendGrid sandbox. | Live Stripe Production (`sk_live_...`); SendGrid production SMTP. |
| **Deployment** | Instant local reload (`npm run dev` / live server). | Triggered automatically on every push / PR to `main`. | Deployed upon merging PR to `main`. | Deployed manually via semantic version tags (`v1.0.0`). |

---

## 2. Environment Variables & Secret Management

### A. Environment Naming Taxonomy
```bash
# Application Configuration
NODE_ENV=development # local | ci | staging | production
PORT=3000
APP_URL=http://localhost:3000

# Database Configuration
DATABASE_URL=postgresql://eventra_user:eventra_pass@localhost:5432/eventra_dev
REDIS_URL=redis://localhost:6379

# Cryptographic & Auth Secrets
JWT_SECRET=super_secret_dev_jwt_key_32_chars_min
HMAC_QR_SECRET=dev_qr_signing_secret_key_change_in_prod

# External Integrations (Stripe & Email)
STRIPE_SECRET_KEY=sk_test_51MockStripeKeyForDev
STRIPE_WEBHOOK_SECRET=whsec_MockStripeWebhookSecret
SMTP_HOST=localhost
SMTP_PORT=1025
```

### B. Core Secret Management Principles
1. **Never Commit Secrets**: `.env`, `.env.local`, and private keys are strictly listed in `.gitignore`.
2. **Standard `.env.example` Blueprint**: Commit an explicit `.env.example` containing dummy key structures to instruct developers on required variables.
3. **Runtime Environment Validation**: Application validates 100% of required environment variables at server boot using a Zod schema (`envSchema.parse(process.env)`). Server fails immediately if a key is missing.

---

## 3. Local Development & Docker Usage Boundaries

> [!NOTE] Hybrid Local Architecture
> To maximize developer velocity, **Docker Compose is used exclusively for stateful infrastructure dependencies** (PostgreSQL & Redis), while application code runs natively with instant hot-reloading (`npm run dev`).

```
[ Local Developer Environment ]
  ├── Native Application Process (Node.js / Live Server) -> Fast HMR Reload
  └── Docker Compose Infrastructure Container Layer
        ├── Container 1: PostgreSQL 15 (Port 5432)
        └── Container 2: Redis 7 (Port 6379)
```

### Docker Boundary Rules
- **DO USE DOCKER FOR**: PostgreSQL database, Redis cache, local mail catchers, and staging/production multi-container orchestration.
- **DO NOT USE DOCKER FOR**: Basic HTML5/CSS3 Task 1 UI static preview (use native local browser server to avoid container build overhead).

---

## 4. Definition of Done (DoD) — Sub-Phase 1.19
- [x] All 4 environment tiers (Local, CI, Staging, Production) defined across Purpose, Configuration, Data Policy, Secrets, External Services, and Deployment.
- [x] Environment variable taxonomy and 3 core secret management principles established.
- [x] Hybrid Local Development Strategy and Docker usage boundaries documented.
- [x] Saved to [`docs/19_ENVIRONMENT_STRATEGY.md`](file:///Users/firozansari/Downloads/Coading/Intership/docs/19_ENVIRONMENT_STRATEGY.md).
