# The Elite Claude Code Playbook

**Your complete system for becoming a Claude Code power user.**

Skills. Commands. Hooks. Audit system. CLAUDE.md templates. Everything you need — ready to drop into your project.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

---

## What's Inside

```
elite-claude-playbook/
├── skills/                    # On-demand context (auto-loads when relevant)
│   ├── project-audit/         # Score your setup out of 100
│   ├── design-system/         # UI/styling standards
│   ├── api-patterns/          # API route conventions
│   └── content-writer/        # Marketing & content creation
├── commands/                  # Slash command templates
│   ├── catchup.md             # Session start status report
│   ├── deploy-check.md        # Pre-deploy verification
│   ├── pr.md                  # Automated PR workflow
│   ├── plan.md                # Implementation planning
│   └── migrate.md             # Safe database migrations
├── hooks/
│   └── hooks-config.json      # Auto-format, safety guards, notifications
└── templates/                 # CLAUDE.md starter templates
    ├── claude-md-webapp.md    # Web app (Next.js / React)
    └── claude-md-mobile.md   # Mobile app (React Native / Expo)
```

## Quick Install

**Install everything at once** — copy the skills, commands, and hooks into your project:

```bash
# Clone the repo
git clone https://github.com/santoshushasrinivasa/elite-claude-playbook.git

# Copy skills into your project
cp -r elite-claude-playbook/skills/* your-project/.claude/skills/

# Copy commands into your project
cp -r elite-claude-playbook/commands/* your-project/.claude/commands/

# Copy hooks config (merge with existing if you have one)
cp elite-claude-playbook/hooks/hooks-config.json your-project/.claude/settings.json

# Restart Claude Code
cd your-project && claude
```

**Or install individual pieces** — grab just what you need:

```bash
# Just the audit skill
cp -r elite-claude-playbook/skills/project-audit your-project/.claude/skills/

# Just the hooks
cp elite-claude-playbook/hooks/hooks-config.json your-project/.claude/settings.json
```

---

## The Philosophy

Elite performance doesn't come from elite prompts. It comes from elite structure.

LLMs mirror your organization. Give Claude a messy, unstructured request and you get messy, unstructured code. Give it clear constraints, documented patterns, and defined standards — and it performs like the senior engineer you can't afford to hire.

Three pillars:

1. **Context Engineering** — What you feed Claude matters more than how you ask. A well-structured CLAUDE.md and skills system eliminates 30-50% of wasted tokens from re-explaining context.

2. **Delegation, Not Dictation** — Stop telling Claude *how* to code. Tell it *what* you need and *why*. Use Plan Mode (Shift+Tab twice) before any complex task.

3. **Infrastructure Investment** — CLAUDE.md files, skills, hooks, MCP servers, and slash commands are your force multipliers. 15 minutes of setup saves hours every week.

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

---

## Commands

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

## Hooks

Hooks are automatic guardrails configured in `.claude/settings.json`. Unlike CLAUDE.md rules (advisory), hooks are guaranteed to execute.

The config includes three essential hooks:

### Auto-Format (PostToolUse)

Runs Prettier on every file Claude edits. Consistent formatting without thinking about it.

### Safety Guard (PreToolUse)

Blocks dangerous commands before they execute:

| Blocked | Why |
|---------|-----|
| `rm -rf /` | Deletes entire filesystem |
| `rm -rf .` | Deletes entire project |
| `DROP TABLE` | Destroys database tables |
| `DROP DATABASE` | Destroys entire database |
| `DELETE FROM...WHERE 1` | Deletes all rows |
| `git push --force` | Overwrites remote history |

### Desktop Notification

Sends a macOS notification with sound when Claude needs your attention. No more staring at the terminal.

> **Note:** The notification hook is macOS-only. For Linux, replace with `notify-send`. For Windows, use PowerShell toast notifications.

[Full hooks config →](hooks/hooks-config.json)

---

## CLAUDE.md Templates

CLAUDE.md is the single highest-impact file in your project. It loads into every conversation automatically. These templates give you a production-quality starting point.

### The Golden Rules

1. **Keep it lean.** Under ~150 instructions. Claude Code's system prompt already contains ~50 — bloated files cause instruction-following to degrade.
2. **Only include what Claude gets wrong.** If removing a line wouldn't cause mistakes, cut it.
3. **Use the WHAT / WHY / HOW framework.**
4. **Don't stuff everything in CLAUDE.md.** Use Skills for domain knowledge.
5. **Iterate continuously.** When Claude makes a mistake, add a rule. When it follows one perfectly for weeks, consider removing it.

### Available Templates

- [Web App (Next.js + Supabase + Tailwind)](templates/claude-md-webapp.md)
- [Mobile App (React Native / Expo)](templates/claude-md-mobile.md)

---

## Cost Optimization

Whether you're on a subscription or API, these strategies cut waste dramatically.

### The 80/20 Rule

| Strategy | Token Savings | Effort |
|----------|--------------|--------|
| `/clear` between tasks | 30-40% | Zero |
| Lean CLAUDE.md + Skills architecture | 20-30% | 15 min setup |
| Sonnet for 80% of tasks, Opus for complex only | 30-40% | Discipline |
| Specific prompts over vague ones | 20-50% per interaction | Mindset shift |
| Use `@file` references instead of letting Claude search | 15-25% | Habit |

### Session Discipline

1. **Start fresh:** `/clear` every time you switch tasks
2. **Name sessions:** `/rename feature-auth-flow` before clearing
3. **Target under 30K tokens** per session for best quality
4. **Compact at 70% capacity** — quality degrades at 85%+
5. **Document & Clear:** Have Claude write progress to a `.md` file, `/clear`, then start new session with "Read docs/plans/feature-x.md and continue"

### Context Capacity Rules

- **0-50%:** Work freely
- **50-70%:** Pay attention, consider compacting
- **70-90%:** `/compact` immediately
- **90%+:** `/clear` is mandatory — quality has collapsed

### Model Strategy

| Task | Model | Why |
|------|-------|-----|
| Routine coding, file edits | **Sonnet** | Fast, cheap, handles 80% of work |
| Architecture, complex refactoring | **Opus** | Deep reasoning, multi-file understanding |
| Text formatting, classification | **Haiku** | Cheapest, fastest for trivial tasks |

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

## Security Checklist

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

## Contributing

Found a way to improve a skill? Have a useful command or hook? PRs welcome.

**Guidelines:**
- Skills should be generic (not project-specific) unless in an `examples/` directory
- Commands should work across common stacks
- Include a description of what your contribution does and why it's useful
- Test your skills/commands on at least one real project before submitting

---

## License

MIT — use it, fork it, share it, build on it.

---

## Origin Story

This playbook started as my personal setup for building [ApplyFlow](https://applyflowtracker.com) — a job application tracker I shipped entirely with Claude Code. I audited my setup, scored a 67/100, and spent the next few sessions fixing every gap. The audit skill, fix kit, skills, commands, and hooks in this repo are what came out of that process.

The core insight: **Claude doesn't need better prompts. It needs better structure.** The more organized your project, the more elite the output. Every skill, command, and hook in this repo exists because I made a mistake and built a guardrail so it wouldn't happen again.

If this helps you ship faster, star the repo and tell me what you built.
