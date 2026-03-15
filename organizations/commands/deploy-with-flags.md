Safely deploy a service using feature flags and canary rollout: `/deploy-with-flags $SERVICE $PERCENTAGE`

Usage: `/deploy-with-flags payment-service 10`

This command progressively rolls out a service to a percentage of traffic, monitoring health, and rolling back automatically if errors spike.

## 1. Verify Source Environment

- [ ] Current environment (staging) is healthy
- [ ] All tests passed in CI
- [ ] Security scan passed
- [ ] No critical alerts firing in staging

If any check fails, **STOP** — do not proceed.

## 2. Create Feature Flag

Feature flag naming convention: `[team]-[service]-[feature]-v[version]`

Examples: `payments-refund-v2`, `auth-sso-v1`, `search-elasticsearch-v3`

Define in your feature flag system (LaunchDarkly, Unleash, custom):

```
Flag: payments-refund-v2
Type: Percentage rollout
Initial: $PERCENTAGE (e.g., 10%)
Targeting: All users
Environment: production
Created by: $USER
Ticket: $TICKET_NUMBER
```

## 3. Deploy Code

1. Merge feature branch to main (requires 2 approvals + tests + security scan)
2. Trigger production deployment via CI/CD
3. Confirm deployment successful in logs
4. **Do NOT enable flag yet** — code is deployed but feature is off

## 4. Enable Flag at Low Percentage

1. In feature flag system, enable flag at $PERCENTAGE (default: 10%)
2. Log the timestamp: `[HH:MM UTC] Enabled flag payments-refund-v2 at 10%`
3. Post to #deployments Slack channel:
   ```
   🚀 CANARY DEPLOYMENT
   Service: payments-refund-v2
   Percentage: 10%
   Status: MONITORING
   Rollback: /rollback payments-refund-v2
   ```

## 5. Monitor Health (5 minutes)

Watch these metrics for the next **5 minutes**:

| Metric | Threshold | Action |
|--------|-----------|--------|
| **Error rate** | > 1% (above baseline) | ROLLBACK |
| **Latency p99** | > 150% of baseline | ROLLBACK |
| **Success rate** | < 99% | ROLLBACK |
| **CPU/Memory** | > 80% utilization | ROLLBACK |
| **Database connection pool** | > 80% exhausted | ROLLBACK |

**Where to look:**
- Error rate: DataDog / Sentry error dashboard
- Latency: DataDog APM or Prometheus
- Resource usage: kubectl top / CloudWatch / Datadog infrastructure

**If any metric breaches threshold:**

Immediately rollback:
```
1. Set flag to 0% in feature flag system
2. Post to Slack: "🚨 ROLLBACK: payments-refund-v2 rolled back to 0% — error rate spiked to X%"
3. Create GitHub issue: "Canary rollback — investigate error spike"
4. **Do NOT re-enable** without understanding root cause
```

**If all metrics healthy after 5 minutes:**

Proceed to next step.

## 6. Increase to 50%

1. Increase flag to 50% in feature flag system
2. Log: `[HH:MM UTC] Increased flag payments-refund-v2 to 50%`
3. Monitor health for another **5 minutes** (same metrics as step 5)

**If anything degrades:**
- Rollback to 0% immediately
- Investigate root cause
- Create GitHub issue

**If healthy:**
- Proceed to 100%

## 7. Roll Out to 100%

1. Increase flag to 100% in feature flag system
2. Log: `[HH:MM UTC] Enabled flag payments-refund-v2 to 100%`
3. Monitor for next **15 minutes** (same metrics)

**If healthy after 15 minutes:**

Post success:
```
✅ DEPLOYMENT SUCCESSFUL
Service: payments-refund-v2
Percentage: 100%
Duration: ~25 minutes (10% → 50% → 100%)
Errors: 0
Latency impact: < 5%
Next: Leave flag enabled, monitor for 1 hour, then document lessons
```

**If issues arise:**
- Rollback to 0%
- Post incident timeline to #incidents
- Create GitHub issue with root cause
- Schedule postmortem

## 8. Post-Deployment (1 hour monitoring)

After full rollout, continue monitoring for **1 hour**:

- [ ] Error rate stable
- [ ] Latency stable
- [ ] No customer complaints in support channel
- [ ] Metrics dashboard green

Document in GitHub issue:
```
Deployment completed successfully.
Error rate: X% (baseline: Y%)
Latency impact: +Z%
No incidents.
Monitoring for the next hour.
```

## 9. Flag Lifecycle

**Keep flag enabled for:**
- If new feature: 2 weeks (monitor for edge cases)
- If critical fix: 24 hours (just in case rollback needed)
- If infrastructure change: 1 week (ensure stability)

**Then:** Remove flag from codebase, clean up feature flag system.

```
If flag > 30 days old and no issues:
→ Remove from code
→ Delete from feature flag system
→ Post cleanup notice to #deployments
```

## 10. Rollback Command (Anytime)

If anything goes wrong **at any step**, use:

```
/rollback $FLAG_NAME

This will:
1. Set flag to 0% immediately
2. Post incident to #incidents Slack
3. Create GitHub issue for root cause investigation
4. Notify service owner and on-call engineer
```

---

## Flag Naming Convention (IMPORTANT)

**Format:** `[team]-[service]-[feature]-v[version]`

**Examples:**
- `payments-refund-v2` (payments team, refund feature, version 2)
- `auth-sso-v1` (auth team, SSO feature, version 1)
- `search-elasticsearch-v3` (search team, ES migration, version 3)

**Why:**
- Team context: which team owns this?
- Service: which service affected?
- Feature: what's being deployed?
- Version: 1st or 2nd attempt?

**DO NOT use:**
- `new-feature-123` (unclear scope)
- `temporary-test` (unclear purpose)
- `fix-bug` (which bug?)

---

## Customization

**For your organization:**

1. **Feature flag system:** Replace references with your actual tool (LaunchDarkly, Unleash, custom):
   - Update step 2 with your flag creation process
   - Update step 4 with your flag enable process
   - Update step 10 with your rollback command

2. **Monitoring dashboards:** Replace DataDog/Sentry with your actual tools:
   - Datadog → [your APM tool]
   - Sentry → [your error tracking tool]
   - kubectl top → [your resource monitoring tool]

3. **Slack channels:** Replace #deployments, #incidents with your channels

4. **Thresholds:** Adjust error rate (>1%), latency (>150%), success rate (<99%) based on your SLOs

5. **Monitoring durations:** Adjust 5-minute, 15-minute, 1-hour windows based on your traffic patterns:
   - High-traffic services (millions/day): keep short windows (5, 15, 15)
   - Low-traffic services (thousands/day): extend windows (10, 20, 30)
