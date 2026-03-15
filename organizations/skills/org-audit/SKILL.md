---
name: org-audit
description: Run a comprehensive organisation-level audit on a Claude Code project or codebase. Scores 15 categories from 0-10 (total /150), covering both engineering quality and org-specific concerns: compliance, team health, secrets posture, dependency governance, and cost management. Use when you want to evaluate a team's or organisation's Claude Code setup, security posture, regulatory readiness, or overall engineering maturity.
---

# Org Audit Skill

## Purpose

Audit any Claude Code project from an **organisational** lens. Score 15 categories, identify gaps, and produce a prioritised fix kit tailored to the team's size and regulatory context.

This audit covers everything a growing engineering team needs: not just code quality and CI/CD, but compliance readiness, access control, dependency risk, cost governance, and team onboarding health.

## How to Run

Read all relevant project files before scoring. Every score must be evidence-based — read actual config files, package.json, CI definitions, CLAUDE.md, .env.example, and infrastructure code. Do not guess or assume.

**Output format:** Write the full audit report to `docs/org-audit-report.md` with:
1. Score summary table (all 15 categories with score and one-line verdict)
2. Detailed findings per category (what exists, what's missing, evidence)
3. Prioritised fix kit (ordered by impact-to-effort ratio)
4. Top 5 quick wins (fixable in under 30 minutes)
5. Compliance risk flags (anything that could cause a SOC 2 / HIPAA / GDPR finding)

---

## Category 1: CLAUDE.md Quality (0-10)

CLAUDE.md is the highest-leverage file in any Claude Code project. At org scale, every team should have one.

**What to check:**
- Does `CLAUDE.md` exist at the project root?
- Does it follow the WHAT / WHY / HOW framework?
  - WHAT: Tech stack, project structure, key files, service dependencies
  - WHY: Service purpose, team ownership, compliance level
  - HOW: Build/test/deploy commands, conventions, how to verify changes
- Is it lean? Under ~150 instructions — bloated files degrade instruction-following
- Does it include team-specific context: on-call contact, Slack channel, JIRA project?
- Does it avoid stuffing everything in one file — uses Skills for domain knowledge?
- Are critical rules marked IMPORTANT: or NEVER?
- Does it document the compliance level of this service?

**Scoring guide:**
- 0-2: Missing or just a project description
- 3-4: Exists but no team context, bloated, or missing key commands
- 5-6: Covers basics but missing compliance level, team ownership, or common mistakes
- 7-8: Well-structured, lean, covers WHAT/WHY/HOW plus team context
- 9-10: Tight, iterated, includes service topology, compliance level, and on-call info

---

## Category 2: Skills & Commands (0-10)

Skills and commands prevent re-explaining standards every session and enforce consistency across engineers.

**What to check:**

*Skills (`.claude/skills/`):*
- Do org-relevant skills exist? Look for: api-patterns, design-system, migration-guide, incident-response, access-control, dependency-governance
- Does each skill have a clear trigger description in frontmatter?
- Are skills shared across the team (version-controlled, not per-machine)?

*Commands (`.claude/commands/`):*
- Do essential commands exist?
  - Session start / catchup
  - Pre-deploy check (lint, typecheck, build, tests, security scan)
  - PR creation with required reviewers
  - Planning (implementation plan before coding)
  - Handoff (end-of-session documentation)
  - Security review (pre-merge security checklist)

**Scoring guide:**
- 0-2: No skills or commands configured
- 3-4: 1-2 basic commands, no skills
- 5-6: Some commands, no org-specific skills
- 7-8: 4+ commands + 3+ skills with clear triggers
- 9-10: Full library covering all key workflows, including org-specific skills (incident, access-control, handoff)

---

## Category 3: Hooks Configuration (0-10)

Hooks are deterministic guardrails. At org scale they are your last line of defence before Claude does something destructive.

**What to check:**
- Read `.claude/settings.json` for hook configuration
- Essential hooks for orgs:

| Hook | Event | Purpose |
|------|-------|---------|
| Auto-format | PostToolUse (Write/Edit) | Runs formatter on every file Claude edits |
| Safety guard | PreToolUse (Bash) | Blocks dangerous commands |
| Audit log | PostToolUse (Write/Edit) | Appends file writes to a session log |
| Secret scanner | PreToolUse (Bash) | Blocks commands containing credential patterns |
| Notification | Notification | Desktop or Slack alert |

**Expanded safety block list for orgs:**

| Blocked Pattern | Risk |
|----------------|------|
| `rm -rf /` | Deletes filesystem |
| `rm -rf .` | Deletes project |
| `DROP TABLE` | Destroys DB table |
| `DROP DATABASE` | Destroys entire DB |
| `TRUNCATE TABLE` | Deletes all rows |
| `ALTER TABLE...DROP COLUMN` | Irreversible schema change |
| `DELETE FROM.*WHERE 1` | Deletes all rows |
| `git push.*--force` | Overwrites remote history |
| `git reset --hard` | Discards local changes |
| `kubectl delete` | Destroys K8s resources |
| `terraform destroy` | Tears down infrastructure |
| `aws s3 rm.*--recursive` | Mass-deletes cloud objects |
| `chmod 777` | Unsafe file permissions |

**Scoring guide:**
- 0-2: No hooks
- 3-4: Only auto-format
- 5-6: Auto-format + basic safety guard (original 6 patterns)
- 7-8: Auto-format + extended safety guard + notification
- 9-10: All above + audit logging + secret pattern detection

---

## Category 4: MCP Servers (0-10)

MCP servers extend what Claude can access. Org teams need the right tools connected — not overloaded.

**What to check:**
- Read MCP config (`.claude/mcp.json` or `claude_desktop_config.json`)
- Org-relevant MCP servers:

| MCP Server | Priority | Purpose |
|------------|----------|---------|
| GitHub | Must-have | PRs, issues, CI status without leaving Claude |
| Context7 | Must-have | Up-to-date library docs in prompts |
| Supabase / Database | Must-have | Direct DB access for services using it |
| Playwright | High | Browser automation and E2E testing |
| Slack | High | Team notifications and incident comms |
| Linear / JIRA | High | Issue tracking integration |
| Figma | Medium | Design-to-code for frontend teams |

**Scoring guide:**
- 0-2: No MCP servers
- 3-4: 1-2 servers, but not the most critical ones
- 5-6: GitHub + 1-2 others
- 7-8: GitHub + Context7 + project-specific DB + 1 comms tool
- 9-10: Full stack appropriate to team scope, no unnecessary servers bloating context

---

## Category 5: Project Structure (0-10)

**What to check:**
- Is there clear separation of concerns (routes, services, models, utils)?
- Are environment-specific configs separated?
- Is there a `docs/` directory with ADRs, runbooks, or architecture diagrams?
- Is the repo structure documented in CLAUDE.md?
- For monorepos: are package boundaries clear, with per-package CLAUDE.md files?

**Scoring guide:**
- 0-2: No structure, everything in root
- 3-4: Basic structure but undocumented
- 5-6: Good structure, partially documented
- 7-8: Clean structure, documented in CLAUDE.md, clear ownership
- 9-10: Clean structure + ADRs + runbooks + monorepo CLAUDE.md hierarchy if applicable

---

## Category 6: Security (0-10)

**What to check:**
- No hardcoded secrets in code or committed `.env` files
- `.env` in `.gitignore`, `.env.example` exists with all required vars documented
- Authentication on all protected routes
- Input validation on all API endpoints (Zod, Joi, or equivalent)
- Rate limiting on auth, payment, and AI endpoints
- RLS (Row-Level Security) enabled on all database tables
- HTTPS enforced in production
- Dependency audit: `npm audit` / `pip-audit` / equivalent shows no critical vulnerabilities
- Security headers configured (CSP, HSTS, X-Frame-Options)
- Secrets management uses a vault (AWS Secrets Manager, Doppler, 1Password) — not plain env files in CI

**Scoring guide:**
- 0-2: Hardcoded secrets or no auth on protected routes
- 3-4: Auth exists but missing input validation or RLS
- 5-6: Auth + validation + RLS but missing rate limiting or security headers
- 7-8: All above + HTTPS + no critical vulns + secrets in vault
- 9-10: Comprehensive posture including dependency audits, security headers, vault-managed secrets, and zero critical findings

---

## Category 7: Testing (0-10)

**What to check:**
- Test coverage on critical paths and business logic
- Unit tests for pure functions and utilities
- Integration tests for API routes (not mocked — real DB or test container)
- E2E tests for critical user journeys
- Test CI integration: tests run on every PR, block merge on failure
- Test data management: seed scripts exist, tests don't share state
- Code coverage threshold enforced in CI

**Scoring guide:**
- 0-2: No tests
- 3-4: Some unit tests, no CI integration
- 5-6: Unit + integration tests, CI runs them
- 7-8: Unit + integration + E2E on critical paths, coverage enforced
- 9-10: Full pyramid with coverage gates, test containers, no shared state, tested on every PR

---

## Category 8: Git & CI/CD (0-10)

**What to check:**
- Branch strategy documented (trunk-based, GitFlow, etc.)
- PR template exists with description, testing notes, checklist
- Required reviewers configured in CODEOWNERS or branch protection
- CI runs on every PR: lint, typecheck, test, build
- Deployment is automated (not manual `git push` to production)
- Staging environment exists and is deployed to before production
- Rollback procedure documented

**Scoring guide:**
- 0-2: No CI, manual deploys, no branch protection
- 3-4: Basic CI (lint only) or no branch protection
- 5-6: CI with tests, automated deploy, but no staging or required reviewers
- 7-8: Full CI + staging + required reviewers + CODEOWNERS
- 9-10: All above + automated rollback + deployment notifications + change log generation

---

## Category 9: Performance & Reliability (0-10)

**What to check:**
- Error boundaries in React (or equivalent in other frameworks)
- Loading and error states on all pages/components that fetch data
- No N+1 database queries
- Pagination on all list endpoints
- Lazy loading for code-split chunks and below-fold content
- SLO targets defined (uptime, p99 latency)
- Monitoring and alerting configured (Datadog, Sentry, etc.)

**Scoring guide:**
- 0-2: No error handling, no monitoring
- 3-4: Basic error handling, no monitoring
- 5-6: Error boundaries + monitoring set up, no SLOs
- 7-8: All above + SLOs defined + alerting configured
- 9-10: SLOs, alerting, no N+1 queries, lazy loading, pagination everywhere

---

## Category 10: Cost Efficiency (0-10)

**What to check:**
- `/clear` usage discipline: are engineers using fresh sessions between tasks?
- CLAUDE.md is lean (under 150 lines) — not stuffing everything in
- Domain knowledge moved to Skills, not in CLAUDE.md
- Model selection: Sonnet for routine work, Opus only for complex architecture tasks
- `@file` references used instead of letting Claude search blindly
- Sessions compacted at 70%+ context capacity
- Context capacity awareness: sessions not running at 90%+ for extended periods

**Scoring guide:**
- 0-2: No awareness of token cost or context management
- 3-4: Some awareness but no discipline or tooling
- 5-6: Lean CLAUDE.md + some Skills usage
- 7-8: All above + model discipline + session hygiene practices documented
- 9-10: Optimised Skills architecture, model policy enforced in team norms, cost monitored

---

## Category 11: Compliance & Regulatory (0-10)

**What to check:**
- Is the compliance level of this service documented? (SOC 2, HIPAA, GDPR, PCI-DSS, FedRAMP, none)
- For SOC 2: audit logging enabled, access reviews scheduled, change management documented
- For HIPAA: PHI encrypted at rest and in transit, access logs retained, BAA with vendors
- For GDPR: data retention policy, right-to-erasure capability, DPA with vendors, data map exists
- For PCI-DSS: cardholder data not stored in application DB, tokenisation in place, quarterly scans
- Privacy policy and terms of service exist and are current
- Data classification policy exists (public / internal / confidential / restricted)

**Scoring guide:**
- 0-2: No compliance documentation, unknown data handling
- 3-4: Compliance level identified but no implementation evidence
- 5-6: Basic compliance controls in place for the applicable framework
- 7-8: Controls implemented + documented + reviewed
- 9-10: Controls implemented, evidence collected, audit-ready, reviewed on schedule

---

## Category 12: Team Health & Onboarding (0-10)

**What to check:**
- Is there a team CLAUDE.md shared across all engineers?
- Are org-level skills and commands version-controlled in the repo?
- Is there a documented onboarding guide for new engineers?
- Do new engineers know how to set up Claude Code (MCPs, hooks, CLAUDE.md)?
- Are architecture decisions recorded in ADRs?
- Is there a runbook for common operational tasks?
- Are team conventions (commit format, branch naming, PR titles) documented and enforced?

**Scoring guide:**
- 0-2: No shared Claude Code config, undocumented onboarding
- 3-4: Some docs but not enforced, no ADRs
- 5-6: CLAUDE.md + commands shared, basic onboarding doc
- 7-8: Full onboarding guide + ADRs + shared skills library
- 9-10: All above + runbooks + conventions enforced in CI + measured onboarding time

---

## Category 13: Secrets & Access Control (0-10)

**What to check:**
- No secrets in code or git history (`gitleaks` or `truffleHog` scan)
- All secrets stored in a vault (AWS Secrets Manager, HashiCorp Vault, Doppler, 1Password)
- API keys rotated on a schedule (quarterly minimum)
- Principle of least privilege: service accounts have minimum required permissions
- MFA enforced for all engineers on GitHub, cloud provider, and third-party services
- RBAC implemented in the application: roles defined, permissions checked server-side
- Access review conducted quarterly: unused accounts and tokens removed

**Scoring guide:**
- 0-2: Secrets in code or git history
- 3-4: Secrets in `.env` files but not in git; no rotation schedule
- 5-6: Secrets in vault, no rotation schedule, no access review
- 7-8: Vault + rotation schedule + MFA enforced + RBAC in application
- 9-10: All above + quarterly access review + gitleaks/truffleHog in CI + least-privilege verified

---

## Category 14: Dependency Governance (0-10)

**What to check:**
- Lock file committed (`package-lock.json`, `yarn.lock`, `poetry.lock`, etc.)
- Dependabot or Renovate configured for automated dependency updates
- `npm audit` / `pip-audit` / `cargo audit` runs in CI — blocks merge on critical/high
- License compliance checked: no GPL dependencies in commercial code without review
- Internal package registry used for private packages (not public registry with private names)
- Dependency update policy documented: how long until vulnerabilities must be patched

**Scoring guide:**
- 0-2: No lock file, no audit tooling
- 3-4: Lock file exists, no automated updates or audit in CI
- 5-6: Lock file + Dependabot/Renovate configured
- 7-8: Lock file + automated updates + audit in CI blocking on critical/high
- 9-10: All above + license compliance check + update policy documented + private registry for internal packages

---

## Category 15: Cost & Model Governance (0-10)

**What to check:**
- Is there an org-level API budget or cost cap configured?
- Is model usage monitored per team or per project?
- Is there a documented model selection policy?
- Are engineers using Haiku for trivial tasks, Sonnet for routine dev, Opus for architecture only?
- Is there a process for investigating cost anomalies (sudden spike in usage)?
- Are context management best practices shared across the team?
- Is cost a line item in sprint planning or team budgets?

**Scoring guide:**
- 0-2: No budget, no monitoring, engineers free to use Opus for everything
- 3-4: Some awareness, no monitoring or policy
- 5-6: Model policy documented, no enforcement or monitoring
- 7-8: Model policy + usage monitoring per team
- 9-10: Budget cap + monitoring + anomaly alerting + model policy enforced + cost reviewed quarterly

---

## Generating the Fix Kit

After scoring all 15 categories, generate a prioritised fix kit:

**Priority tiers:**

| Tier | Score Range | Action |
|------|-------------|--------|
| Critical | 0-3 | Fix immediately — significant risk or missing foundation |
| High | 4-5 | Fix this sprint — meaningful gap |
| Medium | 6-7 | Fix next sprint — good to have |
| Low | 8-9 | Polish — diminishing returns |
| Skip | 10 | Already excellent |

For each Critical and High item, provide:
- The specific gap found (with evidence from the files read)
- A copy-pasteable solution (config snippet, command, template section)
- Estimated effort (15 min / 1 hour / half-day / full day)

---

## Stage-Appropriate Advice

Scale priorities to the team's size and context:

| Stage | Team Size | Key Priorities |
|-------|-----------|----------------|
| Early-stage startup | 10-30 engineers | CLAUDE.md quality, hooks, CI/CD, basic security |
| Growth-stage | 30-100 engineers | + Compliance, access control, dependency governance, onboarding |
| Scale-up | 100-300 engineers | + Cost governance, multi-service patterns, team health, ADRs |
| Enterprise | 300+ engineers | All categories at 9-10, audit-ready, compliance certified |

Don't tell a 20-person startup to achieve SOC 2 Type II before they have 100 paying customers. Don't tell a 500-person enterprise that a basic security checklist is sufficient.

---

## How to Use This Skill

**Full org audit:**
```
Run the org-audit skill on this project. Read all files and produce the full report.
```

**Compliance-focused audit:**
```
Run the org-audit skill focusing on Categories 11, 13, and 14 (compliance, secrets, dependencies). We're preparing for SOC 2.
```

**Team health check:**
```
Run the org-audit skill focusing on Categories 1, 2, 12 (CLAUDE.md, skills/commands, team health).
```

**Quick security posture:**
```
Run the org-audit skill for Categories 6 and 13 only (security and access control).
```

---

## Customization

Update this skill with your org's specific context:
- Replace generic compliance frameworks with your actual regulatory obligations
- Add your internal tooling to the MCP server table
- Set minimum passing scores per category to match your org's maturity targets
- Add your internal secret vault tool to Category 13 checks
- Replace Dependabot with your internal dependency management tooling if applicable
