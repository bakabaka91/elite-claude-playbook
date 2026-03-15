# Team: [Team Name]

## 1. Team Identity

- **Team name:** [Product / Backend / Frontend / Data / etc.]
- **Mission:** [One-line: what does this team build or own?]
- **Slack channel:** #[team-slack-channel]
- **Tech lead:** [Name] (@github-handle)
- **Engineering manager:** [Name]
- **Team members:** [N engineers, list if helpful]
- **On-call rotation:** [Link to schedule, PagerDuty, etc.]
- **Weekly standup:** [Days/times]
- **Retro schedule:** [Frequency and day]

## 2. Stack

- [List the primary technologies/frameworks the team uses]
- **Language:** [TypeScript / Go / Python / Rust / etc.]
- **Web framework:** [Next.js / FastAPI / Rails / etc.]
- **Database:** [Postgres / MongoDB / DynamoDB / etc.]
- **Frontend UI:** [React / Vue / Svelte / etc.]
- **Testing:** [Vitest / Jest / pytest / RSpec / etc.]
- **Package manager:** [npm / yarn / pip / cargo / etc.]
- **Other relevant tools:** [Redis, Kafka, ElasticSearch, etc.]

## 3. Project Structure

- `src/` — Source code
  - `components/` — Reusable UI components
  - `services/` — Business logic and API integration
  - `utils/` — Helper functions
  - `pages/` or `routes/` — Page definitions
- `tests/` — Test files (mirror of src/)
- `docs/` — Documentation, ADRs, runbooks
- `migrations/` — Database migrations (if applicable)
- `.github/workflows/` — CI/CD pipelines

## 4. Commands

- `npm run dev` — Start development server
- `npm run build` — Build for production (must complete without errors before merge)
- `npm run test` — Run full test suite
- `npm run lint` — Run ESLint and static analysis
- `npm run typecheck` — Run TypeScript type checker (must pass)
- `npm run format` — Auto-format code with Prettier
- `npm run migrate` — Run pending database migrations
- `npm run start` — Start production server

## 5. Code Conventions

- **Module system:** ES6 modules (import/export), no CommonJS (require)
- **Style:** [Specific style for your language/framework]
- **Naming:** [camelCase for variables/functions, PascalCase for classes/components]
- **Comments:** Comment *why*, not *what*. Code should be self-explanatory.
- **Error handling:** Always catch and handle errors explicitly; don't let them propagate silently
- **TypeScript:** Strict mode enabled, no `any` types, all functions typed
- **Async code:** Use async/await, not callbacks or promise chains
- **Testing:** Write tests for business logic and critical paths (no test for simple getters)

## 6. Git & Review Workflow

- **Branch naming:** `feature/description`, `fix/description`, `chore/description`
- **Commit messages:** Use conventional commits: `feat:`, `fix:`, `chore:`, `docs:`
  - Example: `feat: add user authentication flow`
  - Example: `fix: resolve race condition in cache cleanup`
- **Required reviewers:** [Number of reviewers needed before merge, specific teams if any]
- **Branch protection:** All changes must go through PR review before merge to main
- **PR template:** [Link to template file]
- **Merge strategy:** [Squash / Rebase / Merge commit]
- **Deployment:** PRs merged to main are automatically deployed (or require manual deploy command)

## 7. Testing Standards

- **Minimum coverage:** [70% / 80% / 90%]
- **Critical paths tested:** [e.g., authentication, payments, core business logic]
- **Test types required:**
  - Unit tests — for pure functions and utilities
  - Integration tests — for API routes and database interactions
  - E2E tests — for critical user journeys
- **Running tests:** `npm run test` must pass on every PR
- **Test data:** Use factories or fixtures, avoid shared state between tests
- **Mocking:** [Mock external APIs / use test containers for databases / other]

## 8. Communication & Process

- **Daily standup:** [Time, format: async Slack thread or in-person meeting]
- **Status updates:** [How often, where, what format]
- **Design reviews:** [When needed / on a schedule / who reviews]
- **Questions & help:** Ask in [#team-slack-channel / team Slack thread]
- **Escalation path:** Stuck? Ask [team lead / engineering manager]
- **On-call:** [How often, what's the rotation, who to notify]
- **Incident response:** Follow the `/incident` command in Claude Code

## 9. Compliance & Security

- **Compliance level:** [None / SOC 2 / HIPAA / GDPR / PCI-DSS]
- **Secrets:** Never hardcode — use `.env` file (in .gitignore) and `.env.example` (in git)
- **Authentication:** All protected endpoints require [Session token / JWT / mTLS / other]
- **Input validation:** Use Zod, Joi, or pydantic for all request bodies
- **Database security:**
  - RLS (Row-Level Security) enabled on sensitive tables
  - No raw SQL — use ORM
  - Migrations are idempotent
- **Logging:** Never log PII (emails, phone numbers, SSNs, payment info)
- **Security review:** `/security-review` command before merging sensitive changes

## 10. Common Mistakes to Avoid

- NEVER hardcode secrets or API keys — use vault / env vars
- NEVER commit `.env` file — use `.env.example` with placeholders
- NEVER use `any` types in TypeScript — define actual types
- NEVER skip the test suite before committing
- NEVER console.log in production code — use structured logging
- NEVER trust user input — always validate
- NEVER deploy without running the full build
- NEVER let a PR sit in review for more than 24 hours — get feedback or merge
