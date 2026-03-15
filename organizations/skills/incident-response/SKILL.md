---
name: incident-response
description: Structured incident response workflow from detection through postmortem. Use when an incident is detected, to escalate and communicate, resolve, and generate a postmortem.
---

# Incident Response Skill

## Purpose

Incidents are going to happen. This skill provides a structured, reproducible response workflow that ensures:
- Rapid acknowledgement and escalation
- Clear communication with stakeholders
- Minimal resolution time
- Complete postmortem and learning

## Severity Tiers & Response SLAs

**Severity classification:**

| Tier | Definition | Examples | Response SLA | Resolution SLA |
|------|-----------|----------|--------------|----------------|
| **P0 — Critical** | Service completely down, data loss, security breach | All APIs 500ing, database offline, credentials leaked | 15 min (page on-call) | 4 hours |
| **P1 — High** | Major feature unavailable, significant data corruption, or security incident under investigation | 10% of requests failing, partial data loss, unverified breach | 1 hour (escalate if P0) | 24 hours |
| **P2 — Medium** | Feature partially degraded, workaround exists | Latency spike, intermittent errors, minor feature broken | 4 hours (next business day if after-hours) | 72 hours |
| **P3 — Low** | Cosmetic or minor issue, no user impact | Typo, wrong color on non-critical UI, non-critical log error | No SLA | Will not be prioritised |

---

## Incident Lifecycle

### Phase 1: Detect & Acknowledge

**Trigger events:**
- Alert fires in Sentry, DataDog, PagerDuty, or monitoring tool
- Customer reports an issue
- Engineer notices something wrong
- Automated health check fails

**Immediate actions (first 5 minutes):**

1. **Acknowledge** the incident in your alerting tool (silence further paging)
2. **Post to #incidents Slack channel:** (or equivalent team channel)
   ```
   🚨 INCIDENT DETECTED
   Title: [Brief description]
   Severity: [P0/P1/P2/P3]
   Affected service: [Service name(s)]
   Customer impact: [Number of users affected, if known]
   Detection time: [HH:MM UTC]
   Timeline log: [Thread will start here]
   ```
3. **Create a GitHub Issue** with label `incident` and link to Slack thread
4. **Document the timeline** in a thread or log:
   ```
   [HH:MM UTC] Incident detected — API /checkout returning 500s
   [HH:MM UTC] Alert fired in Sentry, on-call paged
   ```

---

### Phase 2: Escalate & Assemble

**For P0/P1 incidents:**

1. **Page on-call engineer** if not already paged
2. **Identify service owner** — the engineer most familiar with the affected service
3. **Create a war room** if needed: Slack huddle, Zoom call, or Google Meet
4. **Assign incident commander** — the most senior or most-informed person in the room

**For P0 incidents only:**

1. **Notify stakeholders** — product manager, technical lead, CEO (if customer-facing)
2. **Update status page** (Statuspage.io, Incident.io, or internal): "Investigating"
   ```
   We're investigating reports of [service] being unavailable.
   Last update: [timestamp UTC]
   ```

---

### Phase 3: Investigate & Resolve

**Incident commander responsibilities:**

1. **Coordinate investigation** — delegate who checks what
2. **Keep the timeline updated** — every action logged in Slack thread
3. **Escalate as needed** — if no progress in 15 minutes, escalate to tech lead or on-call director

**Common investigation checklist:**

- [ ] Is the service actually down or just slow? (check metrics)
- [ ] Which deployments happened in the last 30 minutes? (roll back?)
- [ ] Are there any errors in logs? (Sentry, app logs, system logs)
- [ ] Is the database healthy? (connection pool, query latency, disk space)
- [ ] Are there any external service dependencies down? (API calls, CDN, auth provider)
- [ ] Is there a known issue or ongoing change?
- [ ] Do we need to scale the service to handle load?

**Resolution actions:**

- Rollback a recent deploy if it correlates with incident start
- Kill a stuck process or restart the service
- Increase resource limits or scale horizontally
- Failover to a backup database or region
- Disable a feature to isolate the fault
- Apply a database hotfix

**Test the fix:**
- Verify in production using a canary or small percentage of traffic
- Run smoke tests against the fixed service
- Confirm metrics have normalised
- Check that customer reports have stopped

---

### Phase 4: Communicate & Notify

**Update status page every 15 minutes while ongoing:**

```
Update [HH:MM UTC]: We've identified the root cause as a database connection pool exhaustion.
We're applying a fix and expect resolution within 5 minutes.
```

**When resolved:**

```
Resolved [HH:MM UTC]: The incident has been resolved. Database connection pool was restarted.
We're monitoring closely for any recurrence.
Root cause: High query volume from a scheduled job exceeded connection limits.
Followup: We'll implement connection pooling better practices by [date].
```

**Post-incident communication to customers (P0/P1 only):**

Email or in-app notification:
```
We experienced a service outage from [start time] to [end time] UTC on [date].
Root cause: [Brief explanation in customer-friendly terms]
Impact: [Number of users affected, duration]
What we're doing to prevent this: [Brief list of preventive actions]
Status page: [Link]
Thank you for your patience.
```

---

### Phase 5: Resolve & Declare All Clear

**Definition of "resolved":**
- The service is responding normally
- Metrics are within normal ranges
- No errors in the last 5 minutes
- Customers can use the affected feature without degradation

**Declare all clear in Slack:**
```
✅ INCIDENT RESOLVED
Title: [incident title]
Duration: [HH:MM] (start time to end time)
Root cause: [Single sentence]
Affected users: [N]
Postmortem scheduled: [Date/time for meeting]
Action items assigned: [Who is responsible for what]
```

**Close the GitHub Issue** with a link to the postmortem

---

### Phase 6: Postmortem & Learning

**Timeline (schedule within 24-48 hours for P0, within 1 week for P1):**

1. **Write the postmortem document** to `docs/incidents/YYYY-MM-DD-{title}.md`
2. **Invite all participants** (incident commander, service owners, anyone from the war room)
3. **Run a blameless postmortem meeting** — focus on systems and processes, never on individuals

**Postmortem document structure:**

```markdown
# Incident Postmortem: [Title]

## Incident Summary
- **Date/Time:** [YYYY-MM-DD HH:MM UTC]
- **Duration:** [minutes]
- **Severity:** P0/P1/P2
- **Root Cause:** [One sentence — what actually went wrong]
- **Customer Impact:** [Number of users, duration, lost revenue if applicable]

## Timeline
[HH:MM UTC] Event 1 — [What happened]
[HH:MM UTC] Event 2 — [What happened]
...
[HH:MM UTC] Resolution — [What fixed it]

## Root Cause Analysis
[Narrative explanation of the chain of events. Why did this happen? What conditions had to exist?]

## Contributing Factors
- [Something that made it harder to detect or resolve]
- [Another thing that delayed resolution]

## Preventive Actions (What we're doing to ensure this doesn't happen again)

| Action | Owner | Due Date | Tracking |
|--------|-------|----------|----------|
| Implement database connection pool limits | Alice | 2024-03-20 | JIRA-1234 |
| Add alerting for query latency > 5s | Bob | 2024-03-18 | JIRA-1235 |
| Add load test to CI that simulates peak traffic | Carol | 2024-03-25 | JIRA-1236 |

## Lessons Learned
- [Something good that happened]
- [Something we should do differently next time]

## Appendix: Raw Logs / Metrics
[Links to Sentry errors, DataDog dashboard, CloudWatch logs, etc.]
```

**Follow-up actions:**

1. Assign owners to each preventive action
2. Add to sprint backlog — don't let them slip into the backlog void
3. Schedule a review in 30 days — was the action completed? Did it work?

---

## Customization

Update this skill with your organisation's context:
- Replace Slack channel name with your incident-reporting channel
- Replace alerting tools (Sentry, DataDog, PagerDuty) with your stack
- Replace status page tool with your status page provider
- Add your organisation's escalation contacts (on-call rotation, tech lead, CEO contact info)
- Adjust response SLAs to match your service level agreements with customers
- Add your incident postmortem tool (Google Docs, Notion, GitHub, Jira)
