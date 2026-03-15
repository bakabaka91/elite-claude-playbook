---
name: cost-governance
description: Manage Claude API costs at the organisation level. Set budgets, monitor per-team usage, enforce model policies, and detect cost anomalies. Use when setting team budgets, investigating usage spikes, or defining model selection policy.
---

# Cost Governance Skill

## Purpose

Claude API costs scale with team size. This skill provides a framework for budgeting, monitoring, and controlling costs without slowing down engineering.

## Organisation-Level Budget Framework

### Setting the Org Budget

1. **Estimate monthly usage:**
   ```
   Base cost = (number of engineers) × (estimated monthly spend per engineer)
   Add 20% for growth and experimentation

   Example: 50 engineers × $200/month = $10,000 + 20% = $12,000/month
   ```

2. **Set budget tiers:**
   ```
   Soft cap: $11,000/month — alert if exceeded
   Hard cap: $13,000/month — additional approval required above this
   Emergency cap: $15,000/month — only with VP/Eng approval
   ```

3. **Communicate to teams:**
   ```markdown
   # Claude API Budget — FY2024

   - Total org budget: $12,000/month
   - Per-engineer allocation: $200/month
   - Soft cap: $11,000/month
   - Hard cap: $13,000/month

   If your team exceeds allocation, you need VP/Eng approval.
   See cost dashboard: [link to DataDog/Grafana dashboard]
   ```

---

## Per-Team Cost Allocation

Allocate budgets to teams based on usage patterns:

| Team | Engineers | Allocation | Reason |
|------|-----------|-----------|--------|
| Backend | 20 | $5,000/month | Heavy API changes, architecture reviews |
| Frontend | 15 | $2,500/month | UI work, component refinement |
| Data | 10 | $2,000/month | Data pipeline, SQL optimization |
| DevOps | 5 | $1,500/month | Infrastructure code, Terraform |
| Total | 50 | $11,000/month | — |

**Rules:**
- Each team gets a monthly allocation
- Allocations reset on the 1st of the month
- Teams that exceed 80% of allocation get a Slack warning
- Teams that exceed 100% need to escalate for additional budget
- Shared usage (org-wide infrastructure, CI) comes from a "common" pool (10% of total budget)

---

## Model Selection Policy

The single biggest cost lever is which model you use.

### Model Costs & Characteristics

(Approximate as of March 2026)

| Model | Cost | Speed | Reasoning | Use Case |
|-------|------|-------|-----------|----------|
| **Haiku 4.5** | $0.80/MTok input, $4/MTok output | Fastest | Basic | Classification, simple formatting, code review scripts |
| **Sonnet 4.6** | $3/MTok input, $15/MTok output | Fast | Good | Routine coding, file edits, testing |
| **Opus 4.6** | $15/MTok input, $75/MTok output | Slower | Best | Architecture, complex refactoring, multi-file understanding |

### Cost-Conscious Model Selection

**For routine tasks (use Sonnet 4.6):**
- File edits and refactoring
- Writing tests
- Code review
- Bug fixes
- Scaffold new features
- ~80% of daily work

**For complex tasks (use Opus 4.6):**
- Architecture design
- Multi-service refactoring
- Investigation of complex systems
- Risk assessment
- Plan review
- ~10% of daily work

**For trivial tasks (use Haiku 4.5):**
- Format code
- Classify or categorise
- Simple string transformations
- ~10% of daily work

**Never use Opus for:**
- Routine coding
- Simple formatting
- Writing documentation
- Anything that Sonnet would handle just fine

### Policy Enforcement

**Option 1: Honour system**
```
Model Policy: Use Sonnet by default. Switch to Opus only for complex architecture/design tasks.
Switch to Haiku only for trivial formatting tasks. If you're unsure, ask your tech lead.
```

**Option 2: Automated enforcement**
```bash
# In .claude/settings.json
{
  "hooks": {
    "PreCompact": [
      {
        "matcher": "user_prompt",
        "hooks": [{
          "type": "command",
          "command": "if grep -q 'opus' ~/.claude/session.log; then echo 'Opus used this session. Reason: [provide reason in next message]'; fi"
        }]
      }
    ]
  }
}
```

**Option 3: Cost dashboard review**
- Weekly cost review by tech lead
- Identify engineers using Opus excessively
- 1:1 conversation: "I see you used Opus 8 times this week. Tell me about those tasks?"

---

## Cost Monitoring & Dashboards

### What to Track

**Per-engineer:**
- Monthly spend
- Model usage breakdown (% Haiku / Sonnet / Opus)
- Request count and average cost per request
- Peak usage times

**Per-team:**
- Team total spend
- Team average spend per engineer
- Comparison to allocation

**Org-level:**
- Total spend vs budget
- Burn rate (if trending over budget)
- Model usage trends (is Opus use increasing?)
- Spike detection (did someone's spend 5x in one day?)

### Dashboard Setup

**Option 1: DataDog (if you use it for other monitoring)**

```
Dashboard: Claude API Cost Tracking
Widgets:
1. Org spend this month vs budget (line chart)
2. Per-team allocation vs actual (bar chart)
3. Model usage breakdown (pie chart)
4. Cost anomaly alerts (list)
5. Top 10 engineers by spend (table)
```

**Option 2: Grafana + Prometheus**

Export Claude API usage from your Anthropic account to Prometheus, then visualise in Grafana.

**Option 3: Spreadsheet (simple)**

```
Monthly cost tracking in Google Sheets:
- Column A: Date
- Column B: Org spend
- Column C: Cumulative
- Column D: Budget
- Alert: =IF(C > D, "OVER BUDGET", "OK")
```

### Anomaly Detection

Set up alerts:
- If any engineer's spend 3x typical weekly spend in one day → Slack alert
- If org spend 20% above burn rate → Slack alert
- If Opus use 10%+ of spend → Slack alert to VP/Eng

---

## Cost Reduction Strategies

**For engineering teams:**

1. **Session discipline** — `/clear` between unrelated tasks saves 30-40% tokens
2. **Lean CLAUDE.md** — keep it under 150 lines, move domain knowledge to Skills
3. **@file references** — use `@filename` instead of letting Claude search
4. **Context awareness** — compact at 70%, clear at 90%+
5. **Specific prompts** — vague prompts cause re-asking, costing more tokens

**For platform/ops:**

1. **Batch Claude Code sessions** — not every minute of work needs Claude active
2. **Use Haiku for CI/CD scripting** — cost-effective for automated code review, formatting
3. **Cache prompts** — if you run the same analysis weekly, cache it across sessions
4. **Regular audits** — quarterly review of who's using Claude and why

---

## Cost Investigation & Escalation

**If you notice a spike in costs:**

1. **Get the facts:**
   ```bash
   # Query your Anthropic dashboard
   - Who? [Engineer(s) with elevated usage]
   - When? [Day/time of spike]
   - What model? [Was it Opus or normal?]
   - How much? [Tokens, cost]
   ```

2. **Reach out:**
   ```
   Slack message to engineer:
   "Hi! I noticed your Claude usage spiked to $[amount] on [date].
   I see you used [model] [N] times.

   Was this expected? Did you encounter a bug?
   Is there a way to reduce context or use a cheaper model?

   Let me know if you need help optimizing."
   ```

3. **Escalate if needed:**
   - If spike is over $100/day and unexplained → tech lead
   - If pattern repeats → VP/Eng conversation
   - If intentional (e.g., large migration) → document and approve

---

## Quarterly Cost Review

Schedule every 90 days:

```markdown
# Claude API Cost Review — [Quarter]

## Usage Summary
- Total spend: $[amount]
- Budget: $[amount]
- Variance: [+/- X%]
- Per-engineer average: $[amount]

## Model Usage
- Haiku: [%]
- Sonnet: [%]
- Opus: [%]

## Trends
- Is Opus usage increasing? [Yes/No, reason?]
- Any teams over allocation? [List]
- Any anomalies detected? [List]

## Actions This Quarter
- [Action 1 and owner]
- [Action 2 and owner]

## Findings & Recommendations
- Recommendation 1: [e.g., Tighten Opus approval process]
- Recommendation 2: [e.g., Roll out session discipline training]

## Next Review: [90 days from now]
```

---

## Customization

Update this skill with your organisation's context:

- Replace budget numbers with your actual organisation budget
- Replace team allocation table with your org structure
- Add your actual cost per-model (query your Anthropic account)
- Link to your cost dashboard (DataDog, Grafana, etc.)
- Add your Slack channel for cost alerts
- Add your Anthropic account / billing contact
- Replace model selection policy with your org's actual policy
