# Service: [Name]

## 1. Service Identity

- **Service name:** [service-name]
- **Purpose:** [One-line mission: what does this service do?]
- **Owner team:** [Team/squad name]
- **Tech lead:** [Name] (@github-handle)
- **On-call rotation:** [Link to on-call schedule, PagerDuty, etc.]
- **Slack channel:** #[service-slack-channel]
- **JIRA project:** [PROJECT-KEY](https://jira.company.com/browse/PROJECT)
- **Status page:** [Link to status page if customer-facing]
- **Compliance level:** [None / SOC 2 / HIPAA / GDPR / PCI-DSS / FedRAMP]

## 2. Stack

- **Language:** [Go / Python / TypeScript / Rust / etc.]
- **Framework:** [Echo / FastAPI / Express / Axum / etc.]
- **Database:** [Postgres / MongoDB / DynamoDB / etc.]
- **Cache:** [Redis / Memcached / ElastiCache / none]
- **Message broker:** [Kafka / RabbitMQ / AWS SQS / none]
- **Container runtime:** [Docker / containerd]
- **Orchestration:** [Kubernetes / ECS / App Engine / none]
- **Deployment:** [GitHub Actions / GitLab CI / CircleCI / manual]

## 3. Service Topology

- **Upstream services** (services that call this one):
  - [Service A] calls POST `/api/v1/reserve`
  - [Service B] consumes events: `order.created`

- **Downstream services** (services this one calls):
  - Calls [Payment Service] at `https://payment-service.internal/api/v1/charge`
  - Consumes events from [User Service]: `user.created`, `user.deleted`

- **External APIs** (third-party services):
  - Stripe API (`https://api.stripe.com/v1/...`)
  - SendGrid API (email sending)

- **Environment URLs:**
  - Dev: `https://[service].dev.company.internal`
  - Staging: `https://[service].staging.company.internal`
  - Production: `https://[service].company.internal`

- **Ports:**
  - HTTP: 8080
  - Metrics (Prometheus): 9090
  - Debug/logs: none exposed

## 4. Commands

- `npm run dev` — Start dev server (watch mode, hot reload)
- `npm run build` — Production build (no TypeScript errors allowed)
- `npm run test` — Run test suite
- `npm run test:unit` — Unit tests only
- `npm run test:integration` — Integration tests (requires real DB)
- `npm run lint` — Run ESLint
- `npm run typecheck` — Run TypeScript check
- `npm run migrate` — Run pending database migrations
- `npm run seed` — Seed test data
- `npm run docker:build` — Build Docker image
- `npm run docker:run` — Run Docker container locally

## 5. Code Conventions

- **Language style:** [specific to language; e.g., ES modules only, no `require()`]
- **Authentication pattern:** [JWT in Authorization header / Session token in HttpOnly cookie / mTLS / API key]
- **Error format:** Return JSON with `{ error: string, code: string, status_code: number }`
- **Logging format:** JSON structured logs: `{ timestamp, level, message, request_id, service, [context] }`
- **PII policy:** NEVER log user emails, phone numbers, SSNs, or payment info. Log user IDs and anonymous context only.
- **Comment style:** Only comment *why*, not *what*. Code should be self-explanatory.

## 6. API Contract

- **Documentation:** [Link to OpenAPI/Swagger spec]
- **Base URL:** `https://[service].company.internal/api/v1`
- **Authentication:** [Required auth method]
- **API Versioning:** URL-based (`/api/v1/`, `/api/v2/`)
- **Deprecation policy:** v[N] supported for 6 months after v[N+1] release
- **Response format:** `{ data: T, error?: { message, code } }`
- **Pagination:** Offset-based with `limit` and `offset` query params

## 7. Testing Requirements

- **Minimum coverage:** [70% / 80% / 90%]
- **Critical paths tested:** [list: authentication, payment, exports, etc.]
- **Test types required:** unit, integration, E2E (specify which)
- **Test framework:** [Vitest / Jest / pytest / etc.]
- **Database:** [Use real test database / spin up container / in-memory]
- **Running tests:** `npm run test` must pass before merge

## 8. Compliance & Security

- **Compliance obligations:** [SOC 2 / HIPAA / GDPR / PCI-DSS / none]
- **Secrets management:** Use [AWS Secrets Manager / HashiCorp Vault / Doppler / 1Password]
- **No hardcoded secrets:** Check `.env` and `.env.example` — `.env` should NOT be committed
- **RLS on databases:** All tables must have Row-Level Security policies (if using Postgres/Supabase)
- **Input validation:** Use [Zod / Joi / pydantic] for all request bodies, query params, path params
- **Rate limiting:** Enabled on auth, payment, and expensive endpoints
- **Audit logging:** [Required / not required]
  - If required: log all state changes to `audit_log` table with user ID, action, timestamp

## 9. Deployment & Release

- **Release schedule:** [On-demand / weekly / monthly]
- **Deployment environments:** Dev → Staging → Production
- **Required approvals:** [Tech lead / VP/Eng / Change Advisory Board]
- **Rollback procedure:** [How to roll back in case of issues]
- **Monitoring post-deploy:** [Monitor for 15 minutes / 1 hour / etc.]
- **On-call escalation:** If something breaks in production, page on-call engineer via [PagerDuty / etc.]

## 10. Common Mistakes to Avoid

- NEVER hardcode secrets, API keys, or credentials — use vault
- NEVER commit `.env` file — use `.env.example` with placeholders
- NEVER use `console.log` in production code — use structured logging
- NEVER trust request input — always validate with schema
- NEVER skip authentication on protected endpoints
- NEVER commit migrations that modify committed migrations
- NEVER deploy without running the full test suite
- NEVER use raw SQL — always use ORM or parameterised queries
