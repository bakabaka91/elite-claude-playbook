---
name: project-audit
description: Run a comprehensive audit on any Claude Code project. Scores 10 categories from 0-10 (total /100), identifies gaps, and generates a prioritized fix kit with copy-pasteable solutions. Use when you want to evaluate and improve your Claude Code setup, developer experience, security posture, or project quality.
---

# Elite Project Audit Skill

## Purpose

Audit any Claude Code project across 10 categories. Produce a score out of 100, identify gaps, and generate a prioritized fix kit with actionable solutions.

Elite performance comes from elite structure. This audit shows you where the structure is missing.

## How to Run

When this skill is invoked, execute the full audit below. Read the actual project files — don't guess or assume. Every score must be evidence-based.

**Output format:** Write the full audit report to `docs/audit-report.md` with:
1. Score summary table (all 10 categories)
2. Detailed findings per category (what exists, what's missing, evidence)
3. Prioritized fix kit (ordered by impact-to-effort ratio)
4. Top 3 quick wins (things fixable in under 15 minutes)

---

## Category 1: CLAUDE.md Quality (0-10)

CLAUDE.md is the highest-leverage file in any Claude Code project. It loads every session and shapes every response.

**What to check:**
- Does `CLAUDE.md` exist at the project root?
- Does it follow the WHAT / WHY / HOW framework?
  - WHAT: Tech stack, project structure, key files
  - WHY: Project purpose, what each part does
  - HOW: Build commands, test commands, conventions, how to verify changes
- Is it lean? (Under ~150 instructions. Claude Code's system prompt already contains ~50 instructions — bloated CLAUDE.md files cause instruction-following to degrade)
- Does it include common mistakes to avoid? (The highest-value section — only include things Claude gets wrong)
- Are critical rules emphasized with "IMPORTANT:" or "NEVER"?
- Does it avoid duplicating information available in skills or other docs?
- Are build/test/lint commands documented?
- Is there a clear git workflow defined?

**Scoring guide:**
- 0-2: Missing or just a project description with no actionable instructions
- 3-4: Exists but bloated, unstructured, or missing key sections
- 5-6: Covers basics but missing conventions, common mistakes, or emphasis on critical rules
- 7-8: Well-structured, lean, covers WHAT/WHY/HOW with clear conventions
- 9-10: Tight, battle-tested, every line earns its place, iterated based on real mistakes

**Also check for:**
- `CLAUDE.local.md` for personal overrides (should be in .gitignore)
- `~/.claude/CLAUDE.md` for global preferences
- Subdirectory CLAUDE.md files in monorepos

---

## Category 2: Skills & Commands (0-10)

Skills provide on-demand context that loads only when relevant. Commands are reusable prompt templates. Together they prevent re-explaining standards every session and save significant tokens.

**What to check:**

*Skills (`.claude/skills/`):*
- Do any project-specific skills exist?
- Does each skill have a clear `SKILL.md` with a descriptive name and trigger description?
- Are skills focused on a single domain?
- Do skills reference actual project files rather than generic advice?
- Is domain knowledge moved OUT of CLAUDE.md and INTO skills?

*Commands (`.claude/commands/`):*
- Do any slash commands exist?
- Essential commands to look for:
  - Session start / catchup
  - Pre-deploy check
  - PR creation
  - Planning
  - Test generation

**Scoring guide:**
- 0-2: No skills or commands configured
- 3-4: 1-2 basic commands, no skills
- 5-6: Some commands exist but no skills, or skills exist but are generic
- 7-8: 3+ skills with clear triggers, 4+ commands covering key workflows
- 9-10: Comprehensive skill + command library tailored to the project

---

## Category 3: Hooks Configuration (0-10)

Hooks are deterministic guardrails — unlike CLAUDE.md rules (advisory), hooks are guaranteed to execute.

**What to check:**
- Read `.claude/settings.json` for hook configuration
- Essential hooks:

| Hook | Event | Purpose |
|------|-------|---------|
| Auto-format | PostToolUse (Write/Edit) | Runs formatter on every file Claude edits |
| Safety guard | PreToolUse (Bash) | Blocks dangerous commands |
| Notification | Notification | Desktop alert when Claude needs attention |

**Scoring guide:**
- 0-2: No hooks configured
- 3-4: One hook (usually auto-format)
- 5-6: Auto-format + one other hook
- 7-8: Auto-format + safety guard + notification
- 9-10: All three essential hooks plus project-specific hooks

---

## Category 4: MCP Servers (0-10)

MCP servers connect Claude Code to external tools. The right MCPs eliminate context-switching. Too many bloat context.

**What to check:**
- Check `.claude/settings.json` for MCP configuration
- Evaluate based on project stack:

| Stack Component | Recommended MCP |
|----------------|----------------|
| GitHub repos | GitHub MCP |
| Supabase / Postgres | Supabase or PostgreSQL MCP |
| Library docs | Context7 |
| Browser testing | Playwright MCP |
| Figma designs | Figma MCP |

**Scoring guide:**
- 0-2: No MCP servers configured
- 3-4: 1 MCP not critical to workflow
- 5-6: 1-2 relevant MCPs
- 7-8: 2-3 well-chosen MCPs matching workflow
- 9-10: Right MCPs, not overloaded, wildcard permissions for trusted servers

**Important:** More is NOT better. Score DOWN for 5+ MCPs that bloat context without clear value.

---

## Category 5: Project Structure (0-10)

Clean structure helps both humans and Claude navigate the codebase.

**What to check:**
- Scan for files over 500 lines (flag over 300 as warnings)
- Clear directory organization
- Code duplication
- Monorepo: packages clearly separated
- Clear separation: UI, business logic, API, configuration

**Scoring guide:**
- 0-2: No clear structure, large monolithic files
- 3-4: Basic structure, multiple files over 500 lines
- 5-6: Good directories but some large files
- 7-8: Clean separation, most files under 300 lines
- 9-10: Modular, every directory has clear purpose, no file over 500 lines

---

## Category 6: Security (0-10)

Score based on what is actually implemented, not planned.

**What to check:**

*Auth:* All API routes behind authentication? Role-based access? Token validation?

*Data:* RLS enabled? Env vars for secrets? `.env` gitignored? `.env.example` exists? No PII in logs?

*Validation:* Request bodies validated (Zod/Joi)? SQL injection prevention (ORM)? XSS prevention?

*Headers:* CSP, X-Frame-Options, X-Content-Type-Options? CORS not wildcard? Rate limits on auth/payment/AI endpoints?

*Dependencies:* Run `npm audit` — any critical vulnerabilities?

**Scoring guide:**
- 0-2: No auth on routes, no RLS, hardcoded secrets
- 3-4: Auth exists but inconsistent
- 5-6: Auth + RLS + env vars, but missing validation or headers
- 7-8: Solid auth, RLS, validation, env management, basic headers
- 9-10: Comprehensive — all the above plus rate limiting plus clean audit

---

## Category 7: Testing (0-10)

Not about 100% coverage. About whether the paths that matter are tested.

**What to check:**

*Infrastructure:* Test runner configured? Tests in CI?

*Critical paths:* Auth flows, payment webhooks, core API routes, input validation, business logic, data integrity.

*E2E:* Playwright/Cypress configured? Critical user flows tested?

**Scoring guide:**
- 0-2: No tests
- 3-4: Test runner + 1-5 test files
- 5-6: Critical auth and API paths tested, in CI
- 7-8: Good unit + integration + E2E for core flows
- 9-10: Comprehensive with coverage thresholds

**Count:** Total API routes vs. routes with tests. Below 30% = score capped at 5.

---

## Category 8: Git & CI/CD (0-10)

**What to check:**

*Git:* Branching strategy? Conventional commits? PR template? Branch protection?

*CI/CD:* Config exists? Runs typecheck + lint + tests + build? Auto-deploy on merge? Preview environments?

**Scoring guide:**
- 0-2: No CI/CD
- 3-4: Basic CI (lint or build only)
- 5-6: CI runs lint + typecheck + build
- 7-8: Full suite + auto-deploy + previews
- 9-10: Full pipeline + branch protection + security audit + rollback

---

## Category 9: Performance & Production Readiness (0-10)

**What to check:**

*Errors:* Global error boundary? Structured API errors? User-facing error states?

*Loading:* Skeleton loaders? Suspense boundaries? No blank states?

*Performance:* Optimized images? No N+1 queries? Caching? Reasonable bundle size?

*Production:* Env-specific config? Error monitoring? Analytics? Meta tags?

**Scoring guide:**
- 0-2: No error boundaries, no loading states
- 3-4: Basic error handling, no monitoring
- 5-6: Error boundaries + loading states + optimized images
- 7-8: Above + monitoring
- 9-10: Comprehensive — monitoring, analytics, optimized bundle, caching

---

## Category 10: Cost Efficiency (0-10)

**What to check:**

*CLAUDE.md:* Line count (over 200 = concern, over 300 = problem). Loads large files unconditionally? Token impact (~12 tokens/line).

*Context:* Domain knowledge in skills (on-demand) vs CLAUDE.md (always)? Conditional reading? Duplicates between CLAUDE.md and skills?

*Workflow:* Uses @file references? Specific prompts? Model switching strategy?

**Scoring guide:**
- 0-2: CLAUDE.md over 300 lines, loads multiple files every session
- 3-4: Moderately bloated, no skills
- 5-6: Reasonable size, some skills
- 7-8: Lean CLAUDE.md, skills handle domain knowledge
- 9-10: Optimized — lean, comprehensive skills, conditional loading, model strategy

---

## Generating the Fix Kit

### Prioritization Rules

1. Sort by impact-to-effort ratio
2. Group into tiers:
   - **Tier 1: Do Today** — under 15 min, zero/low risk
   - **Tier 2: Do This Week** — 30-60 min, low/medium risk
   - **Tier 3: Do This Month** — 1+ hours, may need staging
   - **Tier 4: Backlog** — nice-to-have
3. For each fix include: what to change, how (copy-pasteable), risk level, production impact, dependencies

### Stage-Appropriate Advice

| Stage | Users | Priority |
|-------|-------|----------|
| Pre-launch | 0 | Foundation: CLAUDE.md, skills, hooks, basic security |
| Early | 1-50 | Ship features, maintain security basics |
| Growing | 50-500 | Testing, performance, monitoring |
| Scale | 500+ | Comprehensive security, E2E testing, optimization |

**IMPORTANT:** Always note "best practice" vs "critical for current stage." Don't recommend a day of test writing when the developer should be shipping features.

---

## Report Template

```markdown
# Project Audit Report

**Project:** [name]
**Date:** [date]
**Overall Score:** [X]/100

## Score Summary

| Category | Score | Status | Notes |
|----------|-------|--------|-------|
| CLAUDE.md Quality | X/10 | 🟢/🟡/🔴 | |
| Skills & Commands | X/10 | 🟢/🟡/🔴 | |
| Hooks Configuration | X/10 | 🟢/🟡/🔴 | |
| MCP Servers | X/10 | 🟢/🟡/🔴 | |
| Project Structure | X/10 | 🟢/🟡/🔴 | |
| Security | X/10 | 🟢/🟡/🔴 | |
| Testing | X/10 | 🟢/🟡/🔴 | |
| Git & CI/CD | X/10 | 🟢/🟡/🔴 | |
| Performance | X/10 | 🟢/🟡/🔴 | |
| Cost Efficiency | X/10 | 🟢/🟡/🔴 | |

🟢 8-10 | 🟡 5-7 | 🔴 0-4

## Detailed Findings
## Fix Kit (Tier 1-4)
## Top 3 Quick Wins
```

---

## Usage

```
# Full audit
Run the project-audit skill. Read all relevant files and produce the full report.

# Single category
Run project-audit — Security category only.

# Re-audit
Run project-audit again. Compare against docs/audit-report.md.

# New project
Run project-audit. Fresh project — focus on foundation.
```
