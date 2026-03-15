# Solo Builders & Small Teams Track

**For 1-50 engineers: Individual developers, startups, and small product teams.**

Everything you need to ship faster with quality guardrails. No bloated governance — just the essentials.

---

## What's Inside

```
solo-builders/
├── skills/                    # On-demand context skills
│   ├── project-audit/         # Score your setup out of 100
│   ├── design-system/         # UI/styling standards
│   ├── api-patterns/          # API route conventions
│   ├── content-writer/        # Marketing & content creation
│   └── migration-guide/       # Safe database migration workflow
│
├── commands/                  # Slash command templates
│   ├── catchup.md             # Session start status report
│   ├── deploy-check.md        # Pre-deploy verification
│   ├── pr.md                  # Automated PR workflow
│   ├── plan.md                # Implementation planning
│   └── migrate.md             # Safe database migrations
│
├── hooks/
│   └── hooks-config.json      # Auto-format, safety guards, notifications
│
└── templates/                 # CLAUDE.md starter templates
    ├── claude-md-webapp.md    # Web app (Next.js / React)
    └── claude-md-mobile.md    # Mobile app (React Native / Expo)
```

---

## Quick Install

**Copy everything into your project:**

```bash
# Clone the elite-claude-playbook repo
git clone https://github.com/bakabaka91/elite-claude-playbook.git

# Copy all solo builder content
cp -r elite-claude-playbook/solo-builders/skills/* your-project/.claude/skills/
cp -r elite-claude-playbook/solo-builders/commands/* your-project/.claude/commands/
cp elite-claude-playbook/solo-builders/hooks/hooks-config.json your-project/.claude/settings.json

# Copy templates (optional)
cp -r elite-claude-playbook/solo-builders/templates/* your-project/docs/templates/

# Restart Claude Code
cd your-project && claude
```

**Or install individual pieces:**

```bash
# Just the audit skill
cp -r elite-claude-playbook/solo-builders/skills/project-audit your-project/.claude/skills/

# Just the hooks
cp elite-claude-playbook/solo-builders/hooks/hooks-config.json your-project/.claude/settings.json
```

---

## The Philosophy

Elite performance doesn't come from elite prompts. It comes from **elite structure**.

LLMs mirror your organization. Give Claude a messy, unstructured request and you get messy, unstructured code. Give it clear constraints, documented patterns, and defined standards — and it performs like the senior engineer you can't afford to hire.

### Three Pillars

1. **Context Engineering** — What you feed Claude matters more than how you ask. A well-structured CLAUDE.md and skills system eliminates 30-50% of wasted tokens from re-explaining context.

2. **Delegation, Not Dictation** — Stop telling Claude *how* to code. Tell it *what* you need and *why*. Use Plan Mode (Shift+Tab twice) before any complex task.

3. **Infrastructure Investment** — CLAUDE.md files, skills, hooks, MCP servers, and slash commands are your force multipliers. 15 minutes of setup saves hours every week.

---

## Skills Overview

### Project Audit

**The flagship skill.** Scores your Claude Code project across 10 categories (each 0-10). Generates a prioritized fix kit with copy-pasteable solutions.

**Categories:** CLAUDE.md Quality, Skills & Commands, Hooks, MCP Servers, Project Structure, Security, Testing, Git & CI/CD, Performance, Cost Efficiency.

[Full skill →](skills/project-audit/SKILL.md)

### Design System

Auto-loads when you're doing UI work. Enforces design tokens, Tailwind-only styling, semantic HTML, accessibility standards, and component reuse.

[Full skill →](skills/design-system/SKILL.md)

### API Patterns

Auto-loads when creating API routes. Enforces: authenticate → validate → business logic → response. Requires Zod validation, ORM usage, proper status codes, PII-free logging.

[Full skill →](skills/api-patterns/SKILL.md)

### Content Writer

Auto-loads for marketing and content tasks. Templates for blog posts, LinkedIn posts, email newsletters with platform-specific formatting and SEO optimization.

[Full skill →](skills/content-writer/SKILL.md)

### Migration Guide

Auto-loads when working on database schema changes. Full workflow: check schema → create migration → write SQL → update ORM → test → deploy → verify.

[Full skill →](skills/migration-guide/SKILL.md)

---

## Commands Overview

### `/catchup` — Start Every Session Here

Reviews branch changes, uncommitted work, lint errors, type errors. Gives you a concise status report.

```
/catchup
```

### `/deploy-check` — Pre-Deploy Verification

Runs typecheck, lint, build, tests. Scans for hardcoded secrets and console.logs. Reports: READY TO DEPLOY or BLOCKED.

```
/deploy-check
```

### `/pr` — Ship Clean Pull Requests

Cleans debug code, runs checks, fixes failures, creates conventional commit, pushes, opens PR with description and testing notes.

```
/pr
```

### `/plan` — Think Before You Code

Creates detailed implementation plan (goal, acceptance criteria, files, edge cases, testing). You review first before implementing.

```
/plan add real-time notifications for job status changes
```

### `/migrate` — Safe Database Migrations

Reads schema, creates migration files, writes idempotent SQL with RLS and indexes, updates ORM, shows SQL for review.

```
/migrate add a tags table with many-to-many relationship to jobs
```

---

## Hooks

Automatic guardrails configured in `hooks/hooks-config.json`.

**What's included:**

- **Auto-Format:** Runs Prettier on every file Claude edits
- **Safety Guard:** Blocks dangerous commands (rm -rf /, DROP TABLE, git push --force, etc.)
- **Desktop Notification:** macOS notification when Claude needs attention

[Full config →](hooks/hooks-config.json)

---

## CLAUDE.md Templates

CLAUDE.md is the single highest-impact file. It loads into every conversation automatically.

### The Golden Rules

1. **Keep it lean:** Under ~150 instructions
2. **Only include what Claude gets wrong**
3. **Use WHAT / WHY / HOW framework**
4. **Don't stuff everything in CLAUDE.md — use Skills**
5. **Iterate continuously**

### Available Templates

- [Web App (Next.js + Supabase + Tailwind)](templates/claude-md-webapp.md)
- [Mobile App (React Native / Expo)](templates/claude-md-mobile.md)

---

## Cost Optimization

### The 80/20 Rule

| Strategy | Token Savings | Effort |
|----------|--------------|--------|
| `/clear` between tasks | 30-40% | Zero |
| Lean CLAUDE.md + Skills | 20-30% | 15 min |
| Sonnet 80%, Opus 20% | 30-40% | Discipline |
| Specific prompts | 20-50% | Mindset |
| `@file` references | 15-25% | Habit |

### Session Discipline

1. `/clear` every time you switch tasks
2. `/rename feature-name` before clearing
3. Target under 30K tokens per session
4. `/compact` at 70% capacity
5. Document progress, `/clear`, resume with context

### Model Strategy

| Task | Model | Why |
|------|-------|-----|
| Routine coding | **Sonnet** | Fast, cheap, 80% of work |
| Complex architecture | **Opus** | Deep reasoning |
| Text tasks | **Haiku** | Cheapest |

---

## Security Checklist

Use before every deploy:

**Security:**
- [ ] No hardcoded API keys or secrets
- [ ] Environment variables for sensitive config
- [ ] `.env` in `.gitignore`, `.env.example` exists
- [ ] RLS enabled on database tables
- [ ] Input validation on API endpoints
- [ ] Authentication on protected routes
- [ ] Rate limiting on auth, payment, AI endpoints

**Quality:**
- [ ] TypeScript — zero `any` types
- [ ] All tests passing
- [ ] Build succeeds
- [ ] No console.log in production code
- [ ] Error boundaries in React
- [ ] Loading and error states on all pages

**Performance:**
- [ ] Images optimized
- [ ] No N+1 queries
- [ ] Bundle size checked
- [ ] Lazy loading for below-fold content

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
| `/clear` | Fresh context |
| `/compact` | Summarize context |
| `/cost` | Show token usage |
| `/model` | Switch models |
| `/permissions` | View/change permission mode |
| `/hooks` | Configure hooks |
| `/rename` | Name current session |
| `/resume` | Resume a named session |

---

## Next Steps

1. **Pick one skill** — Start with `/project-audit` to score your setup
2. **Set up CLAUDE.md** — Use a template as your starting point
3. **Install hooks** — Copy `hooks-config.json` into your project
4. **Use `/catchup`** — Start every session with status check
5. **Use `/pr`** — Ship clean pull requests

---

## For Organizations

Looking for multi-team governance, SLO tracking, incident response, and compliance?

→ See [organizations/ →](../organizations/README.md) for 50-150+ engineer scale-ups.

