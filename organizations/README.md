# Elite Claude Code Playbook — Organisations Edition

**Claude Code at team scale. Governance. Compliance. Cost control. Incident response.**

This folder contains everything engineering teams and organisations need to deploy Claude Code safely, predictably, and cost-effectively. From onboarding engineers to auditing security posture, from managing incidents to controlling API costs — it's all here.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](../../LICENSE)

---

## What's Inside

```
organizations/
├── README.md                           # This file
├── skills/                             # 9 organisation-specific skills
│   ├── org-audit/SKILL.md              # 15-category org maturity audit
│   ├── incident-response/SKILL.md      # Structured incident lifecycle
│   ├── access-control/SKILL.md         # RBAC and IAM patterns
│   ├── dependency-governance/SKILL.md  # License compliance and vuln SLAs
│   ├── cost-governance/SKILL.md        # API budget and model selection
│   ├── multi-service-patterns/SKILL.md # API contracts, versioning, events
│   ├── service-dependencies/SKILL.md   # Cross-team dependencies, SLO contracts, blast radius
│   ├── slo-tracking/SKILL.md           # SLO targets, error budgets, dashboards
│   └── data-classification/SKILL.md    # Data tiers, compliance rules, audit
├── commands/                           # 7 organisation-critical commands
│   ├── handoff.md                      # End-of-session documentation
│   ├── incident.md                     # Initiate incident response
│   ├── security-review.md              # Pre-merge security checklist
│   ├── release-notes.md                # Auto-generate release notes
│   ├── deploy-promote.md               # Multi-env deployment gates
│   ├── deploy-with-flags.md            # Safe canary rollout with feature flags
│   └── ci-governance.md                # Branch protection and CI/CD rules
├── hooks/
│   └── hooks-config-org.json           # Extended hooks (audit log, secret scan)
└── templates/                          # 3 standalone CLAUDE.md templates
    ├── claude-md-service.md            # Microservice/backend service template
    ├── claude-md-platform.md           # Platform/infrastructure team template
    └── claude-md-team.md               # Generic team template (any purpose)
```

---

## Philosophy

As engineering teams grow, structure becomes your competitive advantage.

Claude Code is like hiring a senior engineer. But without structure, you're hiring someone without access to your playbook — they'll have to re-learn your conventions, security policies, and deployment procedures every time you ask them to do something.

This playbook provides that structure:

1. **Governance** — Clear roles, permissions, and approval flows. Who can merge to production? Who reviews security changes?
2. **Compliance** — Track SOC 2, HIPAA, GDPR, PCI-DSS requirements. Know your compliance status at a glance.
3. **Cost Control** — Budget API usage per team, enforce model selection policy, detect anomalies.
4. **Incident Response** — Every incident follows the same workflow. Standardised escalation, communication, and postmortem.
5. **Security** — Access control, secrets management, dependency governance, pre-merge security checks.
6. **Knowledge** — Handoff documents, runbooks, architecture decisions. Engineers don't start from scratch.

---

## Quick Install

### Option 1: Install Everything

```bash
# Copy all skills, commands, hooks, and templates into your project
cp -r organizations/skills/* your-project/.claude/skills/
cp -r organizations/commands/* your-project/.claude/commands/
# Merge hooks config with existing (don't overwrite blindly!)
cat organizations/hooks/hooks-config-org.json >> your-project/.claude/settings.json
cp -r organizations/templates/* your-project/docs/claude-templates/
```

### Option 2: Install Selectively

```bash
# Just the org-audit skill
cp -r organizations/skills/org-audit your-project/.claude/skills/

# Just the incident-response skill
cp -r organizations/skills/incident-response your-project/.claude/skills/

# Just the extended hooks
cat organizations/hooks/hooks-config-org.json your-project/.claude/settings.json
```

---

## Skills — Organisation-Level Knowledge

Skills are on-demand context. They load automatically when relevant. This prevents re-explaining policy every session.

### Org Audit (organisations/skills/org-audit/SKILL.md)

Score your Claude Code setup across **15 categories** out of 150 points:

| Category | What's Scored |
|----------|---------------|
| 1-10 | Standard from individual playbook (CLAUDE.md, Skills, Hooks, etc.) |
| 11 | Compliance & Regulatory — SOC 2, HIPAA, GDPR readiness |
| 12 | Team Health & Onboarding — shared CLAUDE.md, ADRs, runbooks |
| 13 | Secrets & Access Control — secret scanning, IAM, access reviews |
| 14 | Dependency Governance — license compliance, vulnerability SLAs |
| 15 | Cost & Model Governance — budget, per-team usage, anomaly detection |

**Run it:**
```
Run the org-audit skill. Audit this entire codebase and produce the full 15-category report.
```

**Output:** `docs/org-audit-report.md` with score, findings, and fix kit prioritised by impact-to-effort.

### Incident Response (organisations/skills/incident-response/SKILL.md)

Structured incident lifecycle: detect → escalate → communicate → resolve → postmortem.

**What it covers:**
- Severity tiers (P0/P1/P2/P3) with response SLAs
- Escalation workflow (who to page, when)
- Status page updates and customer communication
- Timeline logging and postmortem doc generation

**Triggers:**
- Production service down
- Security breach reported
- Data loss detected
- Major feature broken

### Access Control (organisations/skills/access-control/SKILL.md)

RBAC patterns, least-privilege audit, IAM and session token hygiene.

**What it covers:**
- Role definition scaffold (ADMIN, TEAM_LEAD, ENGINEER, VIEWER)
- Permission matrix
- Server-side authorization checks
- API key rotation workflow
- Quarterly access reviews
- Zero-trust architecture patterns

### Dependency Governance (organisations/skills/dependency-governance/SKILL.md)

License compliance, vulnerability patching SLAs, dependency update workflow.

**What it covers:**
- License tiers (allowed vs. conditional vs. restricted)
- CVE severity and SLA (Critical 24h, High 7d, Medium 30d)
- Dependabot/Renovate setup
- Lock file integrity checks
- Internal package versioning and deprecation

### Cost Governance (organisations/skills/cost-governance/SKILL.md)

API budgets, per-team allocation, model selection policy, anomaly detection.

**What it covers:**
- Org-level budget framework
- Per-team cost allocation
- Model policy (Haiku for trivial, Sonnet for routine, Opus for complex)
- Cost monitoring dashboards
- Cost investigation and escalation
- Quarterly cost review

### Multi-Service Patterns (organisations/skills/multi-service-patterns/SKILL.md)

API contract design, versioning, event-driven architecture, service-to-service auth.

**What it covers:**
- Contract-first API design (OpenAPI spec)
- Version lifecycle (6-month deprecation period)
- Event schema with versioning
- Service-to-service authentication (mTLS, service tokens)
- Circuit breakers and retry strategies
- Idempotency keys
- Distributed tracing

### Service Dependencies (organisations/skills/service-dependencies/SKILL.md)

Map cross-service dependencies, SLO contracts, failure modes, and blast radius.

**What it covers:**
- Service dependency matrix (which services depend on which)
- SLO contracts (what SLO do dependencies need?)
- Failure modes and mitigation (what if Service X goes down?)
- Blast radius analysis (how many services are affected?)
- Circuit breaker patterns (graceful degradation)
- Critical path identification (services that must never fail)
- Multi-region coordination

### SLO Tracking (organisations/skills/slo-tracking/SKILL.md)

Define, measure, and enforce Service Level Objectives (SLOs) across services.

**What it covers:**
- SLO target definition (99.9%, 99.5%, etc.) by service criticality
- Error budget framework (how much downtime can we afford?)
- SLO dashboards (real-time tracking)
- Alert rules (alert before SLO breach, not after)
- SLO-driven incident response (SLO miss = automatic postmortem)
- Quarterly SLO planning (use data to decide feature vs stability work)

### Data Classification (organisations/skills/data-classification/SKILL.md)

Classify data by sensitivity level and enforce handling rules.

**What it covers:**
- Data tiers: Public, Internal, Confidential, Restricted
- Handling rules per tier (storage, access, logging, encryption, deletion)
- Compliance mapping (PCI-DSS for payments, HIPAA for health, GDPR for EU users)
- Detection patterns (grep for credit cards, SSNs, API keys)
- Audit checklist (are we following the rules?)

---

## Commands — Workflows at Organisation Scale

Commands are prompt templates that enforce consistency. Run them with `/command-name`.

### /handoff — End-of-Session Documentation

Engineers document their work: completed items, in-progress tasks, blockers, decisions.

**Output:** `docs/handoffs/YYYY-MM-DD-feature-name.md`

**Saves:** The next engineer 1-2 hours of re-context. No more "what were they working on?"

### /incident — Initiate Incident Response

Acknowledge incident, page on-call, create tracking issue, update status page, escalate appropriately.

**Usage:** `/incident P0 "API returning 500 errors"`

**Triggers:** Immediate acknowledgement, Slack notification, GitHub issue, status page update, escalation if P0/P1.

### /security-review — Pre-Merge Security Checklist

Scan for secrets, verify authentication, validate input, check for PII logging.

**Coverage:**
- Secret scanning (gitleaks patterns)
- Authentication required on protected routes
- Input validation (Zod, Joi, pydantic)
- Safe SQL (ORM only, no string concat)
- RLS on new tables
- No PII in logs

**Output:** APPROVED or BLOCKED with specific findings.

### /release-notes — Auto-Generate Release Notes

Read commit history, categorise by type (Features / Fixes / Breaking Changes), generate release notes.

**Output:** `docs/releases/v[VERSION].md`

**Includes:** Migration guide for breaking changes, contributor list, changelog.

### /deploy-promote — Multi-Environment Promotion with Gates

Deploy from dev → staging → production with approval checks, health verification, and rollback procedure.

**Workflow:**
1. Verify source environment health
2. Confirm all approvals (code review, security review, CAB for prod)
3. Run pre-deploy checks (tests, lint, build)
4. Execute deployment
5. Post-deployment verification (health check, metrics)
6. Notify stakeholders

**Triggers rollback if:** Health check fails, error rate spikes, latency degraded, or manual intervention.

### /deploy-with-flags — Safe Canary Rollout with Feature Flags

Safely deploy code using feature flags and progressive rollout (10% → 50% → 100%).

**Workflow:**
1. Deploy code to production (feature flag OFF)
2. Enable flag at 10% of traffic
3. Monitor for 5 minutes (error rate, latency, resource usage)
4. If healthy, increase to 50%, monitor 5 more minutes
5. If healthy, roll out to 100%
6. Monitor for 1 hour
7. Clean up flag after 2 weeks (safe period)

**Automatic rollback if:** Error rate spikes, latency increases, or resource exhaustion detected.

**Benefit:** Deploy multiple services same day without coordinating. Reduce blast radius of bad deployments from "entire service down" to "10% of users affected, 5 minutes to rollback."

### /setup-branch-protection — Define CI/CD Governance Rules

Automatically enforce quality gates, security scans, and approval requirements before code reaches production.

**Sets up:**
- Required status checks: build, test, security scan, lint, typecheck
- Minimum approvals: 2 on main, 1 on staging
- Code owner reviews: tech leads must approve relevant changes
- Secret scanning: block PRs with hardcoded API keys, credentials
- Stale review dismissal: if PR code changes after approval, need new approval

**Branch protection by tier:**
- Main: 2 approvals, all checks required, no force push, no bypass
- Staging: 1 approval, all checks required, admins can bypass for hotfixes
- Develop: 0 approvals, tests/build required, force push allowed

**Benefit:** Prevents "broken code in production" and "secrets committed" incidents. Enforces policy automatically (not honesty system).

---

## Hooks — Deterministic Guardrails

Hooks execute automatically. Unlike CLAUDE.md advice, hooks *always* run.

### hooks/hooks-config-org.json — Extended Safety & Audit

**Extended from individual playbook:**

**PostToolUse (File writes):**
- Auto-format with Prettier (existing)
- Audit log: append every file write to `.claude/audit/session.log` (new)
- PII detector: warn if file contains email patterns, SSNs, phone numbers (new)

**PreToolUse (Bash commands):**
- Extended safety block list:
  - `DROP TABLE`, `TRUNCATE TABLE`, `ALTER TABLE DROP COLUMN` (new)
  - `git reset --hard`, `kubectl delete`, `terraform destroy`, `aws s3 rm --recursive` (new)
  - `chmod 777` (new)
- Secret pattern detection: block commands containing AWS keys, GitHub tokens, API keys (new)

**Notification:**
- Try osascript (macOS), notify-send (Linux), Slack webhook (optional)

**Setup:**
```bash
# Copy hooks config
cp organizations/hooks/hooks-config-org.json your-project/.claude/settings.json

# Set Slack webhook (optional, for team notifications)
export CLAUDE_SLACK_WEBHOOK=https://hooks.slack.com/services/YOUR/WEBHOOK/URL
```

---

## Templates — Standalone CLAUDE.md Starters

Templates are ready-to-use CLAUDE.md files. Copy them, fill in your team's context, commit, and you're done.

### Template: Backend Service (claude-md-service.md)

For any backend service (microservice, API, worker, etc.).

**Sections:**
1. Service Identity (name, owner, on-call, compliance level)
2. Stack (language, DB, message broker)
3. Service Topology (upstream/downstream, external APIs)
4. Commands (dev, test, deploy)
5. Code Conventions (language-specific patterns)
6. API Contract (OpenAPI spec link, versioning)
7. Testing Requirements (coverage, test types)
8. Compliance & Security (RLS, secrets, audit logging)
9. Deployment & Release (schedule, approvals, rollback)
10. Common Mistakes (things that cause bugs)

**Use it:**
```bash
cp organizations/templates/claude-md-service.md your-service/CLAUDE.md
# Edit to fill in your service context
```

### Template: Platform/Infrastructure Team (claude-md-platform.md)

For DevOps, SRE, or infrastructure teams managing cloud/K8s/etc.

**Sections:**
1. Team Identity (owner, on-call, SLO targets, CAB)
2. Stack (IaC tool, cloud provider, container orchestration)
3. Environment Topology (dev/staging/prod account IDs, regions, networks)
4. Commands (terraform, kubectl, CI/CD triggers)
5. Change Management (who approves, change freeze windows)
6. SLO/SLA Definitions (uptime target, RTO, RPO)
7. Incident Response (escalation, runbooks, status page)
8. Monitoring & Alerting (metrics, dashboards, alerts)
9. Security & Compliance (least-privilege, backups, disaster recovery)
10. Common Mistakes (things that cause outages)

**Use it:**
```bash
cp organizations/templates/claude-md-platform.md infra-repo/CLAUDE.md
# Edit to fill in your team context
```

### Template: Generic Team (claude-md-team.md)

For any team — product, backend, frontend, data, etc.

**Sections:**
1. Team Identity (name, mission, slack channel, on-call)
2. Stack (languages, frameworks, databases)
3. Project Structure (folder layout)
4. Commands (dev, test, build, deploy)
5. Code Conventions (style, naming, error handling)
6. Git & Review Workflow (branch naming, commit messages, required reviewers)
7. Testing Standards (coverage, test types)
8. Communication & Process (standups, escalation, on-call)
9. Compliance & Security (secrets, auth, validation, logging)
10. Common Mistakes (things that cause bugs)

**Use it:**
```bash
cp organizations/templates/claude-md-team.md your-team-project/CLAUDE.md
# Edit to fill in your team context
```

---

## Cost Optimisation

All the strategies from the individual playbook, plus org-level controls:

### The 80/20 Rule (Per-Team)

| Strategy | Token Savings | Effort |
|----------|--------------|--------|
| `/clear` between tasks | 30-40% | Zero |
| Lean CLAUDE.md + Skills | 20-30% | 15 min setup |
| Model discipline (Sonnet 80%, Opus 10%) | 30-40% | Policy + training |
| Per-team budget allocation | 10-20% | Setup + monitoring |
| Session compaction at 70% capacity | 15-25% | Discipline |

### Session Discipline — Org-Wide

1. **Start fresh:** `/clear` every time you switch tasks
2. **Name sessions:** `/rename feature-auth-flow` before clearing
3. **Target 30K tokens per session** — quality degrades beyond
4. **Compact at 70%+** — don't let context bloat
5. **Use `/handoff`** before clearing — document progress so it's not lost

### Model Selection Policy

| Task | Model | Cost | Latency |
|------|-------|------|---------|
| Routine coding (80% of work) | Sonnet | $3-15/MTok | ~30s |
| Complex architecture (10%) | Opus | $15-75/MTok | ~60s |
| Trivial formatting (10%) | Haiku | $0.80-4/MTok | ~5s |

**Enforce:** Honour system, or review weekly usage for engineers using Opus excessively.

---

## MCP Servers — Recommended Stack for Orgs

### Tier 1: Must-Have

| MCP Server | Purpose | Org Requirement |
|------------|---------|-----------------|
| **GitHub** | PRs, issues, CI/CD | Required for all code work |
| **Context7** | Up-to-date library docs | Required for accurate coding |
| **Supabase / Database** | Direct DB access | Required if DB-backed |
| **Slack** | Team communication | Recommended for incident comms |

### Tier 2: High-Value

| MCP Server | Purpose | Team |
|------------|---------|------|
| **Playwright** | Browser automation, E2E testing | Frontend teams |
| **Linear / JIRA** | Issue tracking integration | All teams (optional) |
| **Figma** | Design-to-code | Frontend teams |
| **PagerDuty / Incident.io** | On-call integration | SRE/ops teams |

**Best practice:** Choose 3-4 MCPs matching your org's tech stack. More = bloated context.

---

## Security Checklist — Pre-Deploy

Use before **every** production deployment:

**Security:**
- [ ] No hardcoded API keys, secrets, or credentials
- [ ] All secrets in vault (AWS Secrets Manager, Doppler, 1Password)
- [ ] Environment variables documented in `.env.example`
- [ ] RLS enabled on all sensitive database tables
- [ ] Input validation on all API endpoints (Zod, Joi, pydantic)
- [ ] Authentication required on all protected routes
- [ ] Rate limiting on auth, payment, and expensive endpoints
- [ ] No SQL injection (ORM only, parameterised queries)
- [ ] No XSS (user input escaped, React context)
- [ ] Security headers configured (CSP, HSTS, X-Frame-Options)

**Compliance:**
- [ ] Audit logging enabled (if required)
- [ ] Data retention policy enforced
- [ ] DPA in place with third-party services (if GDPR)
- [ ] PII handling compliant with policy (if HIPAA)

**Quality:**
- [ ] TypeScript — zero `any` types, no errors
- [ ] All tests passing
- [ ] Build succeeds
- [ ] No console.log in production code
- [ ] Error boundaries in React
- [ ] Loading and error states on all pages

**Performance:**
- [ ] No N+1 database queries
- [ ] Images optimized
- [ ] Bundle size checked
- [ ] Pagination on large lists

---

## Governance & Maintenance

### Who Maintains This Playbook?

- **Owned by:** [VP/Eng / Tech Lead / Platform team]
- **Reviewed quarterly** — skills/commands updated with learnings
- **Version control:** Keep playbook in your main repo (or dedicated playbook repo)
- **Training:** New engineers onboarded to Claude Code conventions in week 1

### Customising for Your Org

Edit these files to match your org:

**Hooks config:**
- Replace Slack webhook URL with your team's channel
- Add org-specific secret patterns

**Skills:**
- Link to your internal tools (PagerDuty, Datadog, Jira, etc.)
- Replace compliance frameworks with your actual obligations
- Add your escalation contacts and on-call rotation

**Templates:**
- Fill in your actual tech stack
- Link to your internal docs (wiki, runbooks, ADR repo)
- Add your team structure and ownership

---

## Essential Shortcuts

### Keyboard

| Shortcut | Action |
|----------|--------|
| `Shift+Tab` (×2) | Enter Plan Mode — design before coding |
| `Escape` | Interrupt Claude |
| `#` | Add instruction to CLAUDE.md |
| `@filename` | Reference a specific file |

### Slash Commands

| Command | What It Does |
|---------|-------------|
| `/init` | Generate starter CLAUDE.md |
| `/clear` | Fresh context (use constantly between tasks) |
| `/compact` | Summarize context to save tokens |
| `/cost` | Show token usage for this session |
| `/model` | Switch models (sonnet / opus / haiku) |
| `/handoff` | Generate end-of-session handoff doc |
| `/incident` | Initiate incident response |
| `/security-review` | Pre-merge security checklist |
| `/release-notes` | Auto-generate release notes |
| `/deploy-promote` | Promote to next environment |
| `/deploy-with-flags` | Safe canary rollout (10% → 50% → 100%) |
| `/setup-branch-protection` | Configure CI/CD governance rules |

---

## Contributing

Have improvements to the org-level skills, commands, or templates?

**Before submitting:**
- Test on at least one real team project
- Ensure instructions are specific and actionable
- Include examples of usage
- Link to relevant internal tools / docs

**How to contribute:**
1. Fork the repo
2. Edit the relevant skill/command/template
3. Test thoroughly
4. Open a PR with description of what and why

---

## License

MIT — use it, fork it, share it, improve it.

---

## Origin Story

This organisations edition was built from learnings at teams of 20-500 engineers. It addresses gaps in the individual playbook:

- No incident response workflow → incidents were chaotic and unfocused
- No access control patterns → permissions scattered across repos with no audit trail
- No cost governance → API bills were hard to predict and debug
- No compliance tracking → not ready for SOC 2 audits
- No multi-service guidance → inter-service contracts were inconsistent

The skills, commands, and templates here emerged from these real problems. Every piece exists because a team got burned and built a guardrail so it wouldn't happen again.

If this helps your organisation ship faster and more safely, star the repo and share what you built.
