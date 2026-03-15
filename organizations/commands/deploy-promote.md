Promote a deployment from one environment to the next: `$ARGUMENTS`

Usage: `/deploy-promote staging` (to promote from dev to staging)
Usage: `/deploy-promote production` (to promote from staging to production)

1. **Verify source environment is healthy**:
   - Run health checks: `curl https://[source-env].example.com/health`
   - Check metrics: open DataDog/Grafana for the source environment
   - Look for: error rate < 1%, latency p99 < 1000ms, no alerts firing
   - If unhealthy, BLOCKED — fix issues before promoting

2. **Verify all required approvals are present**:
   - [ ] Code reviewed and merged to main
   - [ ] All CI checks passing (tests, lint, typecheck)
   - [ ] Security review approved (if sensitive changes)
   - [ ] Dependency audit passed (no critical vulns)
   - [ ] For production: tech lead approval (comment on the release PR)
   - If any approval missing, BLOCKED — get approval first

3. **Run pre-deploy checks** for the target environment:
   - Lint: all files pass linting
   - TypeScript: no type errors
   - Tests: all tests passing
   - Build: production build succeeds
   - Secrets: all required env vars are set in target environment
   - Database: migrations have been tested or reviewed (if DB changes)

4. **Create deployment record** in your change management system:
   ```markdown
   # Deployment: [Service] to [Environment]

   - **When:** [Current timestamp UTC]
   - **Who:** [Your name]
   - **Version:** [Git commit hash or tag]
   - **Changes:** [Summary of what's being deployed]
   - **Approvals:** [List of reviewers/approvers and their approval]
   - **Rollback plan:** [If needed, what's the rollback procedure?]
   ```

5. **Notify stakeholders** (Slack, email, status page):
   ```
   🚀 DEPLOYING to [environment]
   Version: [commit hash]
   Changes: [Brief summary]
   Expected impact: [None / Minimal / Requires restart / Scheduled downtime]
   Duration: [Estimated time]
   Rollback plan: [Brief description]
   ```

6. **Execute the deployment**:
   - Trigger CI/CD pipeline or manual deployment process
   - Monitor logs for errors
   - Watch metrics dashboard: error rate, latency, resource usage
   - Verify the target environment is healthy after deployment

7. **Post-deployment verification**:
   - Health check passed? `curl https://[target-env].example.com/health`
   - Metrics healthy? (error rate, latency, resource usage)
   - No new errors in error tracking (Sentry, DataDog)?
   - Can you reproduce the key feature that was changed?
   - If anything looks wrong, ROLL BACK immediately

8. **Final notification** (Slack, status page):
   ```
   ✅ DEPLOYMENT COMPLETE
   Version: [commit hash]
   Duration: [HH:MM]
   Status: Healthy
   Next steps: Monitor for 15 minutes
   ```

**Rollback procedure** (if something goes wrong):

1. Identify the rollback version (previous stable release)
2. Re-run deployment with previous version
3. Verify metrics are healthy
4. Notify stakeholders: "Rolled back to [previous version] due to [brief reason]"
5. Create postmortem ticket: "Why did the deployment fail? What do we fix?"

**Do not:** Deploy to production without explicit approval from your tech lead or deployment owner.
