# The Elite Claude Code Playbook

**Your complete system for becoming a Claude Code power user — from solo builders to enterprises.**

Everything you need to ship faster, safer, and with elite-level structure. Ready to drop into your project.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

---

## Choose Your Path

This playbook has **two distinct tracks:**

### 🚀 **Solo Builders & Small Teams** (1-50 engineers)
Individual developers, startups, and small product teams. Focus: shipping fast with quality guardrails.
- **What you get:** Project audit, design system, API patterns, content creation, database migrations
- **Philosophy:** Structure over prompts. Context engineering eliminates wasted tokens.
- **Install path:** Copy `/solo-builders/` skills, commands, and hooks into your project
- **Details:** [solo-builders/ →](solo-builders/README.md)

### 🏢 **Organizations & Scale-ups** (50-150+ engineers)
Governance, compliance, incident response, cost control, multi-service coordination.
- **What you get:** SLO tracking, service dependencies, data classification, feature flags, CI/CD governance
- **Philosophy:** Consistency, safety, and visibility at scale. Team structure mirrors code structure.
- **Install path:** Copy `/organizations/` skills, commands, and hooks into your project
- **Details:** [organizations/ →](organizations/README.md)

---

## Folder Structure

Two parallel paths, clearly organized:

```
elite-claude-playbook/
│
├── solo-builders/             # 🚀 1-50 engineers
│   ├── README.md              # Solo builder guide
│   ├── skills/                # 5 skills
│   │   ├── project-audit/     # Score your setup out of 100
│   │   ├── design-system/     # UI/styling standards
│   │   ├── api-patterns/      # API route conventions
│   │   ├── content-writer/    # Marketing & content creation
│   │   └── migration-guide/   # Safe database migration workflow
│   │
│   ├── commands/              # 5 commands
│   │   ├── catchup.md         # Session start status report
│   │   ├── deploy-check.md    # Pre-deploy verification
│   │   ├── pr.md              # Automated PR workflow
│   │   ├── plan.md            # Implementation planning
│   │   └── migrate.md         # Safe database migrations
│   │
│   ├── hooks/
│   │   └── hooks-config.json  # Auto-format, safety guards, notifications
│   │
│   └── templates/             # 2 CLAUDE.md templates
│       ├── claude-md-webapp.md    # Web app (Next.js / React)
│       └── claude-md-mobile.md    # Mobile app (React Native / Expo)
│
├── organizations/             # 🏢 50-150+ engineers
│   ├── README.md              # Org guide
│   ├── skills/                # 9 skills
│   │   ├── org-audit/         # Compliance + team health + cost
│   │   ├── incident-response/ # P0/P1/P2/P3 workflows
│   │   ├── access-control/    # IAM, RBAC, least-privilege
│   │   ├── dependency-governance/ # License compliance + vulns
│   │   ├── cost-governance/   # Token budgets, chargeback
│   │   ├── slo-tracking/      # Error budgets, reliability
│   │   ├── service-dependencies/ # Blast radius, failure modes
│   │   ├── data-classification/ # PCI/HIPAA/GDPR rules
│   │   └── multi-service-patterns/ # Service contracts, versioning
│   │
│   ├── commands/              # 5 commands
│   │   ├── ci-governance.md   # Branch protection + status checks
│   │   ├── deploy-with-flags.md # Canary rollout + auto-rollback
│   │   ├── incident.md        # Incident response initiation
│   │   ├── security-review.md # Pre-merge security audit
│   │   └── handoff.md         # End-of-session documentation
│   │
│   ├── hooks/
│   │   └── hooks-config-org.json # Extended safety + audit logging
│   │
│   └── templates/             # 3 team CLAUDE.md templates
│       ├── claude-md-service.md   # Microservice
│       ├── claude-md-platform.md  # Platform/infra
│       └── claude-md-team.md      # Generic team
│
├── README.md                  # Main hub (this file)
├── LICENSE
└── .gitignore
```

## Quick Install

### For Solo Builders & Small Teams

**Install everything at once:**

```bash
# Clone the repo
git clone https://github.com/bakabaka91/elite-claude-playbook.git

# Copy all solo-builder skills, commands, hooks
cp -r elite-claude-playbook/solo-builders/skills/* your-project/.claude/skills/
cp -r elite-claude-playbook/solo-builders/commands/* your-project/.claude/commands/
cp elite-claude-playbook/solo-builders/hooks/hooks-config.json your-project/.claude/settings.json

# Restart Claude Code
cd your-project && claude
```

**Or install individual skills:**

```bash
# Just the audit skill
cp -r elite-claude-playbook/solo-builders/skills/project-audit your-project/.claude/skills/

# Just the migration guide skill
cp -r elite-claude-playbook/solo-builders/skills/migration-guide your-project/.claude/skills/

# Just the hooks
cp elite-claude-playbook/solo-builders/hooks/hooks-config.json your-project/.claude/settings.json
```

[Solo builders guide →](solo-builders/README.md)

### For Organizations & Scale-ups

**Install all org governance tools:**

```bash
# Clone the repo
git clone https://github.com/bakabaka91/elite-claude-playbook.git

# Copy all org skills, commands, hooks
cp -r elite-claude-playbook/organizations/skills/* your-project/.claude/skills/
cp -r elite-claude-playbook/organizations/commands/* your-project/.claude/commands/
cp elite-claude-playbook/organizations/hooks/hooks-config-org.json your-project/.claude/settings.json

# Copy team CLAUDE.md templates
mkdir -p your-project/docs/templates
cp elite-claude-playbook/organizations/templates/* your-project/docs/templates/

# Restart Claude Code
cd your-project && claude
```

**Or install specific org skills:**

```bash
# SLO tracking for reliability
cp -r elite-claude-playbook/organizations/skills/slo-tracking your-project/.claude/skills/

# Service dependency mapping
cp -r elite-claude-playbook/organizations/skills/service-dependencies your-project/.claude/skills/

# Data classification (compliance)
cp -r elite-claude-playbook/organizations/skills/data-classification your-project/.claude/skills/
```

[Org guide →](organizations/README.md)

**Or combine both paths** (solo + org):

```bash
# Install all skills (solo + org)
cp -r elite-claude-playbook/solo-builders/skills/* your-project/.claude/skills/
cp -r elite-claude-playbook/organizations/skills/* your-project/.claude/skills/

# Install all commands (solo + org)
cp -r elite-claude-playbook/solo-builders/commands/* your-project/.claude/commands/
cp -r elite-claude-playbook/organizations/commands/* your-project/.claude/commands/
```

---

## The Philosophy

**Elite performance doesn't come from elite prompts. It comes from elite structure.**

LLMs mirror your organization. Give Claude a messy, unstructured request and you get messy, unstructured code. Give it clear constraints, documented patterns, and defined standards — and it performs like the senior engineer you can't afford to hire.

### For Solo Builders & Small Teams

Three pillars:

1. **Context Engineering** — What you feed Claude matters more than how you ask. A well-structured CLAUDE.md and skills system eliminates 30-50% of wasted tokens from re-explaining context.

2. **Delegation, Not Dictation** — Stop telling Claude *how* to code. Tell it *what* you need and *why*. Use Plan Mode (Shift+Tab twice) before any complex task.

3. **Infrastructure Investment** — CLAUDE.md files, skills, hooks, MCP servers, and slash commands are your force multipliers. 15 minutes of setup saves hours every week.

### For Organizations & Scale-ups

Three strategic principles:

1. **Governance Over Growth** — What worked at 10 engineers breaks at 100. Explicit contracts (SLOs, API versions, service dependencies) prevent cascading failures when services proliferate.

2. **Compliance & Cost Visibility** — At scale, compliance (PCI/HIPAA/SOC 2) and token budgets become critical. Policies must be automated, not advisory.

3. **Incident Discipline** — P0/P1/P2/P3 response tiers, postmortems for SLO misses, and blameless culture separate reliable orgs from chaotic ones. Incident response is a skill, not a reaction.

---

## Skills

Skills are directories in `.claude/skills/` with a `SKILL.md` entrypoint. Claude loads them on demand based on their description — only when the work is relevant. This saves significant tokens compared to stuffing everything in CLAUDE.md.

### Project Audit

**The flagship skill.** Scores your Claude Code project across 10 categories, each rated 0-10 (total /100). Generates a prioritized fix kit with copy-pasteable solutions.

**Categories scored:**

| # | Category | What It Checks |
|---|----------|---------------|
| 1 | CLAUDE.md Quality | Structure, leanness, WHAT/WHY/HOW framework |
| 2 | Skills & Commands | On-demand context, workflow automation |
| 3 | Hooks | Auto-formatting, safety guards, notifications |
| 4 | MCP Servers | Right tools connected, not overloaded |
| 5 | Project Structure | File organization, separation of concerns |
| 6 | Security | Auth, RLS, validation, headers, secrets management |
| 7 | Testing | Critical path coverage, CI integration |
| 8 | Git & CI/CD | Branch strategy, automated checks, deployment |
| 9 | Performance | Error handling, loading states, optimization |
| 10 | Cost Efficiency | Token usage, context management, model strategy |

**Run it:**
```
Run the project-audit skill on this project. Read all relevant files and produce the full report.
```

**What makes it different:** It includes stage-appropriate advice. A solo developer with 5 users gets different priorities than a team with 500 users. It won't tell you to spend a day writing tests when you should be shipping features.

[Full skill file →](skills/project-audit/SKILL.md)

### Design System

Auto-loads when you're doing UI work. Enforces your design tokens, Tailwind-only styling, semantic HTML, accessibility standards, and component reuse. Prevents Claude from generating random colors and duplicate components.

[Full skill file →](skills/design-system/SKILL.md)

### API Patterns

Auto-loads when creating or modifying API routes. Enforces a strict route structure: authenticate → validate → business logic → response. Requires Zod validation, ORM usage, proper status codes, and PII-free logging.

[Full skill file →](skills/api-patterns/SKILL.md)

### Content Writer

Auto-loads for marketing and content tasks. Provides templates for blog posts, LinkedIn posts, and email newsletters with platform-specific formatting, SEO optimization, and CTA requirements.

[Full skill file →](skills/content-writer/SKILL.md)

### Migration Guide

Auto-loads when you're working on database schema changes. Provides a full step-by-step workflow: check existing schema → create migration → write SQL → update ORM → test → deploy → verify. Includes common patterns for adding columns, enum values, junction tables, and indexes. Enforces RLS on new tables, idempotent SQL, and never modifying committed migrations.

[Full skill file →](skills/migration-guide/SKILL.md)

---

## Organization Skills (50-150+ engineers)

For teams building at scale. Each skill is enterprise-ready with compliance, SLA enforcement, and cross-team standards.

### SLO Tracking

Define, measure, and enforce Service Level Objectives. Calculates error budgets, tracks SLO misses (automatic postmortem trigger), and uses data-driven reliability targets to decide: ship features or fix stability?

**When to use:** Defining reliability targets for services, investigating SLO breaches, planning quarterly reliability roadmaps.

[Full skill file →](organizations/skills/slo-tracking/SKILL.md)

### Service Dependencies

Map cross-service dependencies, SLO contracts, failure modes, and blast radius. Identifies critical path services that must never fail and ensures graceful degradation when dependencies are down.

**When to use:** Designing new services, refactoring monoliths to microservices, incident post-mortems, understanding cascade failures.

[Full skill file →](organizations/skills/service-dependencies/SKILL.md)

### Data Classification

Classify data by sensitivity tier (public → restricted) and enforce handling rules per compliance framework (PCI-DSS, HIPAA, GDPR, SOC 2). Includes detection patterns for PII, secrets, and compliance violations.

**When to use:** Data governance audits, pre-merge security review, designing new data pipelines, compliance readiness checks.

[Full skill file →](organizations/skills/data-classification/SKILL.md)

### Incident Response

Structured incident lifecycle: detect → acknowledge → escalate → communicate → resolve → postmortem. Severity tiers (P0/P1/P2/P3) with response SLAs, escalation templates, status page updates, and postmortem formats.

**When to use:** Any production incident, on-call training, retrospective analysis of response times.

[Full skill file →](organizations/skills/incident-response/SKILL.md)

### Access Control

RBAC pattern scaffold, least-privilege review checklist, IAM audit steps, API key rotation workflows, and session token hygiene. Enforces zero-trust principles and service-to-service authentication.

**When to use:** Security audits, onboarding new team members, API key rotations, reviewing third-party integrations.

[Full skill file →](organizations/skills/access-control/SKILL.md)

### Dependency Governance

License compliance (MIT/Apache → allowed, GPL → review, proprietary → approval), vulnerability SLAs (Critical 24h, High 7d, Medium 30d), and approved registry policies. Prevents supply chain attacks and license violations.

**When to use:** Dependency audits, evaluating third-party libraries, managing vulnerability disclosures, compliance reviews.

[Full skill file →](organizations/skills/dependency-governance/SKILL.md)

### Cost Governance

Per-team token budget frameworks, model selection policy (Haiku for CI, Sonnet for dev, Opus for architecture), and chargeback models. Monitors cost anomalies and enforces session discipline at org level.

**When to use:** Monthly budget reviews, tracking per-team API usage, enforcing cost controls, investigating cost spikes.

[Full skill file →](organizations/skills/cost-governance/SKILL.md)

### Multi-Service Patterns

Inter-service contracts (OpenAPI spec first), API versioning policy, event-driven patterns, mTLS/service tokens, and shared library governance. Standardizes how services talk to each other.

**When to use:** Designing service architectures, planning breaking API changes, evaluating async vs. sync patterns, shared library updates.

[Full skill file →](organizations/skills/multi-service-patterns/SKILL.md)

### Organization Audit

Extends the solo project audit with org-specific categories: Compliance & Regulatory, Team Health & Onboarding, Secrets & Access Control, Dependency Governance, Cost & Model Governance.

**When to use:** Quarterly org health checks, readiness audits for compliance frameworks (SOC 2, HIPAA), team skill adoption reviews.

[Full skill file →](organizations/skills/org-audit/SKILL.md)

---

## Commands — Solo Builders & Small Teams

Commands are prompt templates in `.claude/commands/`. Run them with `/command-name`.

### `/catchup` — Start Every Session Here

Reviews your branch changes, uncommitted work, lint errors, and type errors. Gives you a concise status report so you know exactly where you left off.

```
/catchup
```

### `/deploy-check` — Pre-Deploy Verification

Runs typecheck, lint, build, and tests. Scans for hardcoded secrets and console.logs. Reports pass/fail for each step with a final verdict: READY TO DEPLOY or BLOCKED.

```
/deploy-check
```

### `/pr` — Ship Clean Pull Requests

Cleans up debug code, runs all checks, fixes failures, creates a conventional commit, pushes, and opens a PR with description and testing notes. Dirty working tree to submitted PR in one command.

```
/pr
```

### `/plan` — Think Before You Code

Creates a detailed implementation plan — goal, acceptance criteria, files to modify, edge cases, testing plan — and writes it to a markdown file. Does NOT implement anything. You review first.

```
/plan add real-time notifications for job status changes
```

### `/migrate` — Safe Database Migrations

Reads existing schema, creates migration files, writes idempotent SQL with RLS and indexes, updates ORM schema, and shows you the SQL for review before applying. Never modifies committed migrations.

```
/migrate add a tags table with many-to-many relationship to jobs
```

---

## Commands — Organizations & Scale-ups

Team-level operational workflows with governance, approval, and audit logging.

### `/setup-branch-protection` — CI/CD Governance

Configures GitHub branch protection rules: 2 approvals on main, required status checks (build, test, security-scan, lint), secret scanning, and blocking force-pushes even for admins.

```
/setup-branch-protection
```

[Full command →](organizations/commands/ci-governance.md)

### `/deploy-with-flags` — Safe Canary Deployments

Progressive rollout with feature flags: 10% → 50% → 100% with automatic rollback on health metric degradation (error rate, latency, resource exhaustion).

```
/deploy-with-flags payment-service v2.5.0
```

[Full command →](organizations/commands/deploy-with-flags.md)

### `/incident` — Incident Response Workflow

Acknowledges incident, creates GitHub issue with template, assigns on-call, starts timeline log, triggers PagerDuty for P0/P1.

```
/incident P1 "Payment API returning 503s - customers can't checkout"
```

[Full command →](organizations/commands/incident.md)

### `/security-review` — Pre-Merge Security Audit

Scans changed files for secrets, API keys, hardcoded credentials, injection vulnerabilities, missing RLS, and compliance violations. APPROVED or BLOCKED with findings.

```
/security-review
```

[Full command →](organizations/commands/security-review.md)

### `/handoff` — End-of-Session Documentation

Summarizes all changes made this session, lists open TODOs, blockers, and next steps. Writes handoff note to `docs/handoffs/YYYY-MM-DD-{feature}.md` for next engineer.

```
/handoff
```

[Full command →](organizations/commands/handoff.md)

---

## Hooks

Hooks are automatic guardrails configured in `.claude/settings.json`. Unlike CLAUDE.md rules (advisory), hooks are guaranteed to execute.

### Solo Builder Hooks

Three essential hooks configured in `hooks/hooks-config.json`:

**Auto-Format (PostToolUse):** Runs Prettier on every file Claude edits. Consistent formatting without thinking.

**Safety Guard (PreToolUse):** Blocks dangerous commands (rm -rf /, DROP TABLE, git push --force, etc.).

**Desktop Notification:** Sends a macOS notification when Claude needs your attention.

[Full hooks config →](hooks/hooks-config.json)

### Organization Hooks

Extended hooks configured in `organizations/hooks/hooks-config-org.json`:

**Auto-Format:** Same as solo.

**Safety Guard:** Extended blocklist including `TRUNCATE TABLE`, `DROP COLUMN`, `git reset --hard`, `kubectl delete`, `terraform destroy`, `aws s3 rm --recursive`, `chmod 777`, and secret patterns (AWS keys, GitHub tokens).

**Audit Logging:** Every file Claude writes is logged to `.claude/audit/YYYY-MM-DD.log` for compliance and accountability.

**PII Detection:** Grep file content for email patterns, phone numbers, SSNs — warns before writing PII to disk.

**Slack Webhook Integration:** POST notifications to `$CLAUDE_SLACK_WEBHOOK` for team awareness (optional, requires env var).

[Full hooks config →](organizations/hooks/hooks-config-org.json)

---

## CLAUDE.md Templates

CLAUDE.md is the single highest-impact file in your project. It loads into every conversation automatically.

### The Golden Rules

1. **Keep it lean.** Under ~150 instructions. Claude Code's system prompt already contains ~50 — bloated files cause instruction-following to degrade.
2. **Only include what Claude gets wrong.** If removing a line wouldn't cause mistakes, cut it.
3. **Use the WHAT / WHY / HOW framework.**
4. **Don't stuff everything in CLAUDE.md.** Use Skills for domain knowledge.
5. **Iterate continuously.** When Claude makes a mistake, add a rule. When it follows one perfectly for weeks, consider removing it.

### Solo Builder Templates

- [Web App (Next.js + Supabase + Tailwind)](templates/claude-md-webapp.md)
- [Mobile App (React Native / Expo)](templates/claude-md-mobile.md)

### Organization Templates

- [Backend Microservice](organizations/templates/claude-md-service.md) — Service identity, stack, topology, API contract, testing, compliance
- [Platform/Infrastructure Team](organizations/templates/claude-md-platform.md) — IaC, environments, change management, SLO/SLA, incident runbooks
- [Generic Team](organizations/templates/claude-md-team.md) — Team identity, structure, commands, code conventions, communication

---

## Cost Optimization

Whether you're on a subscription or API, these strategies cut waste dramatically.

### Individual / Team Strategy (Solo Builders)

The 80/20 rule for solo developers:

| Strategy | Token Savings | Effort |
|----------|--------------|--------|
| `/clear` between tasks | 30-40% | Zero |
| Lean CLAUDE.md + Skills architecture | 20-30% | 15 min setup |
| Sonnet for 80% of tasks, Opus for complex only | 30-40% | Discipline |
| Specific prompts over vague ones | 20-50% per interaction | Mindset shift |
| Use `@file` references instead of letting Claude search | 15-25% | Habit |

**Session Discipline:**
1. `/clear` every time you switch tasks
2. `/rename feature-auth-flow` before clearing
3. Target under 30K tokens per session
4. `/compact` at 70% capacity
5. Document progress, `/clear`, resume with context

**Context Capacity Rules:**
- 0-50%: Work freely
- 50-70%: Consider compacting
- 70-90%: `/compact` immediately
- 90%+: `/clear` mandatory

**Model Strategy:**

| Task | Model | Why |
|------|-------|-----|
| Routine coding, file edits | **Sonnet** | Fast, cheap, 80% of work |
| Architecture, complex refactoring | **Opus** | Deep reasoning, multi-file |
| Text formatting, classification | **Haiku** | Cheapest, fastest |

---

### Organization Strategy (50-150+ engineers)

Org-level cost control requires per-team budgets and model policy enforcement.

**Token Budget Framework:**
- **Haiku:** CI/CD, classification tasks, automated reviews (lowest cost tier)
- **Sonnet:** Routine dev work, feature coding, refactoring (default tier)
- **Opus:** Architecture reviews, complex multi-service changes, incidents (reserved for high-value work)

**Cost Governance Skill:** Use `/cost-governance` to set per-team budgets, monitor usage anomalies, and enforce model policy across organization.

**Chargeback Model Options:**
1. **Central budget** — Fixed monthly cost, teams manage within pool
2. **Per-team allocation** — Each team gets quarterly budget, can't exceed
3. **Per-project allocation** — Critical projects get higher allocations
4. **Usage-based chargeback** — Teams billed internally based on actual usage

**Session Discipline at Scale:**
- Team leads must enforce `/clear` between major features
- Document decisions to `docs/decisions/` before clearing
- Use `/checkpoint` to save progress mid-session
- Audit logs track who used what model for what task

[Full cost governance skill →](organizations/skills/cost-governance/SKILL.md)

---

## MCP Servers — Recommended Stack

### Tier 1: Must-Have

| MCP Server | What It Does |
|------------|-------------|
| **GitHub** | PRs, issues, CI/CD without leaving Claude |
| **Context7** | Injects up-to-date library docs into prompts |
| **Supabase** | Direct database access from Claude |
| **Playwright** | Browser automation and E2E testing |

### Tier 2: High-Value

| MCP Server | Use Case |
|------------|----------|
| **Firecrawl** | Web scraping, clean LLM-ready data |
| **Figma** | Design-to-code workflows |
| **Slack** | Team communication automation |
| **Notion / Linear** | Project management integration |

> **Best practice:** Choose 2-3 MCPs that match your workflow. Too many bloat context and slow startup.

---

## Security Checklist — Solo Builders

Use before every deploy:

**Security:**
- [ ] No hardcoded API keys or secrets
- [ ] Environment variables for all sensitive config
- [ ] `.env` in `.gitignore`, `.env.example` exists
- [ ] RLS enabled on all database tables (Supabase/Postgres)
- [ ] Input validation on all API endpoints
- [ ] Authentication on all protected routes
- [ ] Rate limiting on auth, payment, and AI endpoints

**Quality:**
- [ ] TypeScript — zero `any` types, no errors
- [ ] All tests passing
- [ ] Build succeeds
- [ ] No console.log in production code
- [ ] Error boundaries in React components
- [ ] Loading and error states on all pages

**Performance:**
- [ ] Images optimized
- [ ] No N+1 database queries
- [ ] Bundle size checked
- [ ] Lazy loading for below-fold content

---

## Security Governance — Organizations

Use the `/security-review` command for pre-merge security audits:

**Automated Checks (run every PR):**
- [ ] Gitleaks — no hardcoded secrets (AWS keys, GitHub tokens, API keys)
- [ ] SonarQube / Semgrep — SAST vulnerabilities (SQL injection, XSS, auth bypasses)
- [ ] Dependabot / Snyk — vulnerable dependencies (CVSS ≥ 5.0 blocks merge)
- [ ] License scanning — GPL/proprietary licenses require approval
- [ ] DAST (staging only) — runtime security issues, auth flaws

**Pre-Commit Hooks (local machine):**
- [ ] PII detection — no credit cards, SSNs, email patterns logged
- [ ] Credential scanning — AWS keys, GitHub tokens blocked
- [ ] Conventional commits — enforce commit message format

**Quarterly Audits:**
- [ ] Access control audit — least-privilege review
- [ ] Data classification — audit sensitive data storage
- [ ] Compliance readiness — SOC 2, HIPAA, PCI-DSS, GDPR
- [ ] Incident post-mortems — SLO misses trigger blameless analysis

[Full data classification skill →](organizations/skills/data-classification/SKILL.md)
[Full access control skill →](organizations/skills/access-control/SKILL.md)

---

## Essential Shortcuts

### Keyboard

| Shortcut | Action |
|----------|--------|
| `Shift+Tab` (×2) | Enter Plan Mode |
| `Escape` | Interrupt Claude |
| `#` | Add instruction to CLAUDE.md |
| `@filename` | Reference a specific file |

### Slash Commands

| Command | What It Does |
|---------|-------------|
| `/init` | Generate starter CLAUDE.md |
| `/clear` | Fresh context (use constantly) |
| `/compact` | Summarize context to save tokens |
| `/cost` | Show token usage |
| `/model` | Switch models (sonnet/opus/haiku) |
| `/permissions` | View/change permission mode |
| `/hooks` | Configure hooks |
| `/rename` | Name current session |
| `/resume` | Resume a named session |

---

## How to Choose Your Path

### Choose **Solo Builders** if:
- You're a solo founder or indie hacker
- Your team is 1-20 people
- You prioritize shipping speed over process
- You need basic quality guardrails (linting, testing, security scanning)
- You don't have compliance requirements
- **Install:** Copy root-level `/skills/`, `/commands/`, `/hooks/`

### Choose **Organizations** if:
- Your team is 50-150+ engineers
- You have multiple services / teams
- You need governance and compliance (PCI/HIPAA/SOC 2)
- You track API usage and token costs
- You run incident response workflows
- You need SLO tracking and reliability targets
- **Install:** Copy `/organizations/skills/`, `/organizations/commands/`, `/organizations/hooks/`

### Choose **Both** if:
- You have a core product team (solo builders) + infrastructure/platform team
- You want foundation patterns + enterprise governance
- **Install:** Copy all root-level + all `/organizations/` content (they coexist)

---

## Quick Wins

**For Solo Builders — Pick One:**
1. Start with `/catchup` — know where you left off
2. Use `/deploy-check` before every merge
3. Run `/pr` to ship PRs cleanly

**For Organizations — Pick One:**
1. Start with SLO tracking — define reliability targets
2. Set up CI/CD governance — enforce branch protection
3. Run `/security-review` on sensitive PRs

---

## Contributing

Found a way to improve a skill? Have a useful command or hook? PRs welcome.

**Guidelines:**
- Skills should be generic (not project-specific) unless in an `examples/` directory
- Commands should work across common stacks
- Include a description of what your contribution does and why it's useful
- Test your skills/commands on at least one real project before submitting
- Separate solo builder contributions (root) from org contributions (`/organizations/`)

---

## License

MIT — use it, fork it, share it, build on it.

---

## Origin Story

This playbook started as my personal setup for building [ApplyFlow](https://applyflowtracker.com) — a job application tracker I shipped entirely with Claude Code. I audited my setup, scored a 67/100, and spent the next few sessions fixing every gap. The audit skill, fix kit, skills, commands, and hooks in this repo are what came out of that process.

The core insight: **Claude doesn't need better prompts. It needs better structure.** The more organized your project, the more elite the output. Every skill, command, and hook in this repo exists because I made a mistake and built a guardrail so it wouldn't happen again.

If this helps you ship faster, star the repo and tell me what you built.
