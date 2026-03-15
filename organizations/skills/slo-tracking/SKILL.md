---
name: slo-tracking
description: Define, measure, and enforce Service Level Objectives (SLOs) across services. Track error budgets, alert on SLO violations, and use data-driven reliability targets. Use when defining reliability targets for services, investigating SLO misses, or planning stability work.
---

# SLO Tracking Skill

## Purpose

At 50-150 engineers, you need explicit reliability targets. Without SLOs:
- Teams ship features recklessly ("uptime is someone else's problem")
- You don't know if you're reliable (99.5%? 99.9%? 99.99%?)
- Reliability work is invisible (no metrics to show improvement)
- Incident decisions are emotional, not data-driven

This skill provides:
1. SLO definition (what does "reliable" mean?)
2. Error budget framework (how much downtime can we afford?)
3. SLO dashboard (are we healthy?)
4. SLO-driven incident response (did we miss SLO? Postmortem required)
5. Reliability investment decisions (should we build features or fix reliability?)

---

## How to Run

For each service, define:
1. SLO target (99.5%? 99.9%? 99.95%? 99.99%?)
2. Error budget (how much downtime per month?)
3. Alert threshold (alert at 80% error budget consumed)
4. Dashboard (what metrics measure SLO?)
5. Escalation (if SLO missed, what happens?)

**Output:** Write to `docs/slo-targets.md` with:
1. SLO table (all services, targets, current status)
2. Error budget tracking (consumed this month?)
3. SLO-driven incidents (which caused SLO misses?)
4. Reliability roadmap (what needs to be fixed?)

---

## Step 1: Define SLO Targets by Service Criticality

Different services need different reliability targets:

### Tier 1: Critical Services (99.9% SLO)
These services are essential. If down, revenue stops or users are completely blocked.

```
Payment API: 99.9% uptime
- Revenue-critical: customers can't pay
- SLO: 99.9% monthly uptime (allow 43 minutes downtime/month)
- Error budget: 43 minutes
- Alert threshold: 35 minutes (80% of budget)
```

**Services in Tier 1:**
- Payment/Billing API
- User Authentication
- Core database
- Primary API gateway

### Tier 2: High-Value Services (99.5% SLO)
Major features, but not blocking revenue. Some degradation is acceptable.

```
Order Service: 99.5% uptime
- Feature-critical: customers can't checkout long-term
- SLO: 99.5% monthly uptime (allow 216 minutes / ~3.6 hours downtime/month)
- Error budget: 216 minutes
- Alert threshold: 173 minutes (80% of budget)
```

**Services in Tier 2:**
- Order service
- Shipping service
- Catalog/search
- Dashboard

### Tier 3: Secondary Services (99.0% SLO)
Nice-to-have features. Downtime is annoying but not critical.

```
Analytics: 99.0% uptime
- Non-blocking: if down, site still works
- SLO: 99.0% monthly uptime (allow 432 minutes / ~7.2 hours downtime/month)
- Error budget: 432 minutes
- Alert threshold: 345 minutes (80% of budget)
```

**Services in Tier 3:**
- Analytics
- Notifications
- Email service
- Logging

### Tier 4: Internal/Experimental (95.0% SLO)
Internal tools, experimental features. Downtime is acceptable.

```
Admin Panel: 95.0% uptime
- Internal only: employees, not customers
- SLO: 95.0% monthly uptime (allow ~36 hours downtime/month)
- Error budget: 2160 minutes
- Alert threshold: 1728 minutes (80% of budget)
```

---

## Step 2: Define Metrics for Each SLO

For each service, define what "uptime" means:

### Payment API Example

```
Service: Payment API
SLO Target: 99.9%

Uptime definition:
- Successful requests (HTTP 200) / Total requests
- Exclude: health checks, load balancer pings
- Include: payment processing, refunds, invoice lookup

Measurement window: rolling 30-day month
Calculation: (successful_requests / total_requests) × 100%

Example:
- Total requests this month: 10,000,000
- Successful requests: 9,999,000
- Error rate: 0.01% (1000 failed requests)
- Uptime: 99.99% ✅ (exceeds 99.9% SLO)
```

### Latency-Based SLO Example

```
Service: Search API
SLO Target: p99 latency < 500ms

Measurement:
- 99th percentile of response latency
- Sample: last 10,000 requests
- Success: p99 < 500ms
- Failure: p99 > 500ms

Example:
- Sorted latencies: [1ms, 5ms, ..., 450ms, 455ms, 460ms, 490ms, 495ms, 500ms, ...]
- 99th percentile: ~495ms ✅ (within SLO)
```

### Error Rate SLO Example

```
Service: Order Service
SLO Target: error rate < 0.1%

Measurement:
- 5xx errors / total requests
- Exclude 4xx (client errors, not service fault)
- Sample: last 1 hour

Example:
- Total requests (1 hour): 100,000
- 5xx errors: 50
- Error rate: 0.05% ✅ (below 0.1% SLO)
```

---

## Step 3: Calculate Error Budget

Error budget = total allowed downtime per month

### Formula

```
Error Budget = (1 - SLO_Target) × minutes_per_month

Examples:

99.9% SLO:
Error Budget = (1 - 0.999) × 43,200 = 43.2 minutes/month

99.5% SLO:
Error Budget = (1 - 0.995) × 43,200 = 216 minutes/month

99.0% SLO:
Error Budget = (1 - 0.99) × 43,200 = 432 minutes/month
```

### Error Budget as "Permission to Deploy"

Use error budget to decide: should we ship features or fix reliability?

```
This month's status:
- Payment API SLO: 99.9% (error budget: 43 minutes)
- Error budget consumed: 35 minutes (81% used)
- Remaining: 8 minutes

Decision: STABILITY ONLY
- No new features until SLO recovers
- All hands on reliability work
- Expected recovery: 2 weeks

Alternative scenario:
- Error budget consumed: 5 minutes (11% used)
- Remaining: 38 minutes
- Decision: FEATURE SHIPPING OK (plenty of error budget)
```

---

## Step 4: Set Up SLO Dashboard

Track real-time SLO status:

### Dashboard Template

```
📊 Service SLO Dashboard

┌─────────────────────────────────────────────────────────────┐
│ Payment API                                                 │
├─────────────────────────────────────────────────────────────┤
│ SLO Target: 99.9%                                           │
│ Current (30-day): 99.92% ✅                                 │
│ Error Budget: 43.2 min                                      │
│ Consumed: 31 min (72%)  ⚠️  YELLOW                           │
│ Remaining: 12.2 min                                         │
│ Status: HEALTHY (no alert)                                  │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ Order Service                                               │
├─────────────────────────────────────────────────────────────┤
│ SLO Target: 99.5%                                           │
│ Current (30-day): 98.8% ❌                                   │
│ Error Budget: 216 min                                       │
│ Consumed: 260 min (120%) 🔴 RED                              │
│ Remaining: -44 min (SLO MISSED)                             │
│ Status: BREACH (postmortem required)                        │
└─────────────────────────────────────────────────────────────┘
```

### Where to Build Dashboard

Use existing monitoring tools:
- **DataDog:** SLO module (native SLO tracking)
- **Prometheus:** PromQL queries to calculate uptime
- **New Relic:** SLI/SLO feature
- **Custom:** Query database, calculate uptime, post to Slack

**Example Prometheus query:**

```promql
# Calculate 30-day uptime for Payment API
(1 - sum_over_time(rate(payment_api_errors_total[5m])[30d:]) / sum_over_time(rate(payment_api_requests_total[5m])[30d:])) * 100
```

---

## Step 5: Alert on SLO Violations

Set up alerts **before** SLO is missed, not after.

### Alert Rules

```
Alert: "SLO Error Budget Alert"
Trigger: Error budget > 80% consumed
Severity: WARNING
Action: Slack notification to #reliability

Alert: "SLO Breach"
Trigger: Error budget > 100% consumed (SLO missed)
Severity: CRITICAL
Action: Page on-call, create GitHub issue, trigger postmortem

Alert: "Rapid Error Budget Burn"
Trigger: Error budget consumed > 50% in last hour
Severity: WARNING
Action: Slack + page on-call (something bad is happening NOW)
```

### Slack Alert Example

```
🚨 SLO ERROR BUDGET BURN ALERT

Service: Payment API
Status: 🔴 Critical error budget burn detected

Current status:
- Error rate: 2.5% (SLO allows 0.1%)
- Error budget consumed: 36 of 43 minutes (84%)
- Remaining budget: 7 minutes
- Burn rate: 3.6x (will consume rest of budget in 2 minutes)

Action required:
1. Page on-call engineer: @payment-oncall
2. Investigate: check recent deployments, database health, external APIs
3. Mitigation options: rollback, feature flag disable, scale up

Channel: #reliability
```

---

## Step 6: SLO-Driven Incident Response

If SLO is missed, it's automatic postmortem.

### Rule: SLO Miss = Postmortem

```
If Payment API SLO missed this month:
→ Mandatory postmortem within 48 hours
→ Root cause analysis required
→ Preventive action defined
→ SLO recovery plan created

Example postmortem trigger:
- May 1-31: Payment API uptime = 99.85% (below 99.9% SLO)
- Difference: 0.05% (missed by small margin)
- Action: Schedule postmortem for June 1st
- Questions: Why did we miss? What's the pattern?
```

### SLO Miss Decision Tree

```
Did service miss SLO this month?

├─ YES: SLO MISS
│  ├─ Severity 1: Critical (e.g., 98% instead of 99.9%)
│  │  └─ Action: Immediate postmortem, halt feature work
│  ├─ Severity 2: Major (e.g., 99.85% instead of 99.9%)
│  │  └─ Action: Postmortem within 48 hours, reduce feature velocity
│  └─ Severity 3: Minor (e.g., 99.89% instead of 99.9%)
│     └─ Action: Postmortem, no action required

└─ NO: SLO MET
   ├─ Healthy (< 50% error budget used)
   │  └─ Action: Continue feature shipping
   └─ Warning (50-80% error budget used)
      └─ Action: Monitor closely, plan stability work
```

---

## Step 7: Use SLOs to Plan Reliability Work

Make reliability investment visible and data-driven:

### Quarterly Reliability Planning

```
Q2 SLO Analysis:

Payment API:
- April: 99.92% (error budget: 72% used)
- May: 99.85% (error budget: 100% used) ❌ MISS
- June: 99.88% (error budget: 90% used)
- Pattern: Reliability degrading
- Root cause: 3 incidents from database performance
- Investment needed: Improve database indexing, add monitoring

Order Service:
- April: 99.53% (error budget: 75% used)
- May: 99.61% (error budget: 41% used) ✅
- June: 99.58% (error budget: 60% used)
- Pattern: Healthy, slight variability
- Action: Continue monitoring, no major work needed

Q2 Reliability Roadmap:
1. Payment API database performance (12 engineer-weeks)
   - Add missing indexes
   - Optimize slow queries
   - Upgrade connection pooling
   - Expected impact: +0.1% uptime (98.8% → 99.0% or better)

2. Alerting system overhaul (4 engineer-weeks)
   - Reduce alert noise (false positives)
   - Improve alert actionability
   - Expected impact: -30% on-call pages from false alerts

3. Chaos engineering (6 engineer-weeks)
   - Simulate failure modes
   - Test failover procedures
   - Expected impact: +0.05% uptime improvement, better incident response
```

---

## Step 8: SLO Tiers Decision Framework

**Should Service X be 99.9% or 99.5%?**

Ask:
1. **Revenue impact:** If down 1 hour, lose $X in revenue?
2. **Customer impact:** How many customers affected?
3. **User experience:** Is it a blocker or nice-to-have?
4. **Engineering cost:** Cost to maintain 99.9% vs 99.5%?

### Decision Table

| Factor | → Suggests Higher SLO (99.9%+) | → Suggests Lower SLO (99%+) |
|--------|--------------------------------|---------------------------|
| **Revenue** | $100K+/hour | < $10K/hour |
| **Customer Impact** | 100% of users affected | < 10% of users |
| **Feature Type** | Critical path (payment, auth) | Secondary (analytics, notifications) |
| **Cost to Maintain** | Moderate (extra monitoring, automation) | High (complex, expensive) |
| **On-Call Burden** | Can handle on-call paging | Would burn out on-call |

---

## Step 9: SLO Quarterly Review

Every quarter, review and adjust SLOs:

```
Q2 SLO Review (June 30):

Payment API:
- SLO: 99.9%
- Performance: 99.88% (missed by 0.02%)
- Assessment: ❌ Missing SLO, too aggressive
- Adjustment: Lower to 99.8% (more achievable)
- Reason: Database performance limits, plan upgrade for Q3

Order Service:
- SLO: 99.5%
- Performance: 99.58% (exceeds SLO)
- Assessment: ✅ Healthy
- Adjustment: Keep at 99.5%
- Reason: Team is comfortable with current reliability

Analytics:
- SLO: 99.0%
- Performance: 99.12% (exceeds SLO)
- Assessment: ✅ Very healthy
- Adjustment: Raise to 99.5% (team is capable)
- Reason: Team demonstrated strong reliability, can handle higher SLO
```

---

## Step 10: Communicate SLOs to Teams

Make SLOs visible and understandable:

### Team CLAUDE.md Addition

```markdown
## Service Reliability Target (SLO)

**SLO:** 99.9% monthly uptime

**Error Budget:** 43 minutes per month
- Already consumed: 15 minutes (35%)
- Remaining: 28 minutes
- Status: HEALTHY (no alert)

**What this means:**
- Payment API can be down up to 43 minutes per month and still meet SLO
- If we hit 44 minutes downtime, we miss SLO (triggers postmortem)
- Current error rate: 0.08% (below 0.1% threshold)

**If SLO is missed:**
- Root cause postmortem required
- Feature work pauses for stability sprint
- Prevent repeat incidents with fixes
```

---

## Customization

**For your organization:**

1. **Adjust SLO targets:**
   - Critical payment service: 99.95%? 99.99%?
   - Adjust based on your revenue impact, not industry standard

2. **Define your metrics:**
   - Payment: include refunds? Partial success?
   - Catalog: p99 latency? Error rate? Both?
   - Search: p95 latency? Suggest quality?

3. **Set alert thresholds:**
   - Default: 80% of error budget consumed
   - Adjust based on burn rate patterns
   - Example: for slow, steady burn, alert at 70%; for rapid burn, alert at 90%

4. **Choose dashboard tool:**
   - DataDog SLO module (native)
   - Custom Prometheus queries
   - Grafana dashboards
   - Spreadsheet (simple, but manual)

5. **Escalation contacts:**
   - Replace @payment-oncall with your actual on-call rotation
   - Define escalation: oncall → tech lead → VP/Eng
   - Define postmortem owner

6. **Error budget policy:**
   - Can you "borrow" error budget from next month? (Usually no)
   - What happens if you miss? (Postmortem, stability sprint, compensation?)
   - Are SLO misses public/private?
