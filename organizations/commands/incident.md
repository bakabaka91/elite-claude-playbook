Initiate a structured incident response for: `$ARGUMENTS`

Usage: `/incident P0 "API returning 500 errors on /checkout"`

1. **Acknowledge** the incident immediately:
   - Silence paging in your alerting tool (PagerDuty, DataDog, etc.)
   - Post to #incidents Slack channel with: Severity, affected service, customer impact estimate
   - Pin the message for visibility

2. **Create tracking** — open a GitHub Issue with label `incident`:
   ```
   Title: [P0/P1/P2] [Brief description]

   ## Timeline
   - [HH:MM UTC] Incident detected
   - [Ongoing — will update]

   ## Impact
   - Service(s): [list]
   - Users affected: [estimate]
   - Estimated revenue impact: [if applicable]
   ```

3. **Assemble** the war room:
   - Page on-call engineer if not already paged
   - Notify service owner (the engineer most familiar with the affected system)
   - Create Slack huddle or Zoom call for real-time coordination
   - Assign incident commander (most senior person available)

4. **Update status page** (Statuspage.io, Incident.io, or internal):
   ```
   We're investigating reports of [service] being unavailable.
   Last update: [timestamp UTC]
   ```

5. **For P0 incidents only:**
   - Notify technical leadership (VP/Eng, Eng Manager)
   - Notify product if customer-facing
   - Alert exec team if revenue-impacting

6. **Keep the timeline updated** in the GitHub Issue thread:
   - Log every action with timestamps
   - "15:30 UTC — Identified recent deploy as cause"
   - "15:35 UTC — Rolling back deploy"
   - "15:40 UTC — Deployment rolled back, verifying recovery"

7. **Once resolved:**
   - Update status page: "Resolved. Root cause was [brief explanation]"
   - Update GitHub Issue with resolution timestamp and root cause
   - Close the issue (postmortem will be scheduled separately)
   - Post to #incidents: "✅ INCIDENT RESOLVED — postmortem scheduled for [date/time]"

**Do not:** Leave an incident untracked. Every incident, no matter how small, needs a GitHub Issue and Slack notification so the team knows what's happening.
