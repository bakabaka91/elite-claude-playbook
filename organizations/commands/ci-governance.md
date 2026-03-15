Define CI/CD branch protection rules and automated governance: `/setup-branch-protection`

This command sets up GitHub branch protection rules to enforce quality gates, security scans, and approval requirements before code reaches production.

## 1. Define Protection Rules for Main Branch

Main branch is your source of truth. Protect it.

Run this command to configure:

```bash
gh api repos/your-org/repo/branches/main/protection \
  -X PUT \
  -f required_status_checks='{"strict":true,"contexts":["build","test","security-scan"]}' \
  -f required_pull_request_reviews='{"dismissal_restrictions":{},"dismiss_stale_reviews":true,"require_code_owner_reviews":false,"required_approving_review_count":2}' \
  -f enforce_admins=true \
  -f allow_force_pushes=false \
  -f allow_deletions=false
```

**This enforces:**

| Rule | Enforcement | Reason |
|------|-------------|--------|
| 2 code reviews minimum | Required | Prevents "engineer approves own change" |
| Tests must pass | Required | Catches regressions |
| Build must succeed | Required | Catches compilation errors |
| Security scan must pass | Required | Catches hardcoded secrets, vulnerabilities |
| Dismiss stale reviews | Enabled | If code changes after approval, need new approval |
| Force push blocked | Enabled | Prevents overwriting history |
| Delete branch blocked | Enabled | Prevents accidental deletion |
| Admins not exempt | Enabled | Rules apply to everyone (including leadership) |

## 2. Define Status Checks Required

Your CI/CD system must report these status checks:

**All required (PR cannot merge without passing):**

```
build          — Code compiles / npm run build succeeds
test           — All unit + integration tests pass
security-scan  — No hardcoded secrets, no critical vulns, gitleaks passes
lint           — Linter (ESLint / Prettier) passes
typecheck      — TypeScript or type checker passes (zero errors)
```

**Optional but recommended (PR can merge but you're warned):**

```
coverage       — Code coverage above floor (warn if below 70%)
performance    — Bundle size check (warn if increases >10%)
accessibility  — A11y scan (warn if regressions)
```

## 3. Define Approval Rules by Change Type

Different changes require different approval levels.

**Tier 1: Any engineer can approve (2 reviews total)**
- Documentation updates
- Comment improvements
- Test additions
- Refactoring (no behavior change)

**Tier 2: Tech lead must approve (1+ approval must be tech lead)**
- Feature changes
- API changes (non-breaking)
- Database schema non-breaking changes
- Configuration changes

**Tier 3: VP/Eng or tech lead approval required**
- Breaking API changes
- Database schema breaking changes (requires migration)
- Infrastructure changes
- Third-party integrations
- Payment/billing changes

**Tier 4: Cross-team approval required**
- Changes affecting 2+ services
- Changes to shared libraries
- Changes to core infrastructure

**Implementation:**

Use GitHub CODEOWNERS file to define automatic approvers:

```
# CODEOWNERS
# Format: path @owner

# Tier 1: Any engineer
/docs/**  @your-org/engineers
/tests/** @your-org/engineers

# Tier 2: Tech lead approval required
/src/api/**        @your-org/tech-leads
/src/database/**   @your-org/tech-leads
/src/auth/**       @your-org/tech-leads

# Tier 3: VP/Eng approval required
/infrastructure/** @your-org/vp-engineering
/payments/**       @your-org/vp-engineering

# Tier 4: Cross-team
/shared/**         @your-org/tech-leads
/lib/**            @your-org/tech-leads
```

## 4. Configure Required Reviewers

Set minimum reviewers per team:

```bash
# Example: Backend service requires 2 approvals
gh api repos/your-org/backend/branches/main/protection/required_pull_request_reviews \
  -F required_approving_review_count=2 \
  -F dismiss_stale_reviews=true
```

**Rules:**

- Default: 2 approvals on all PRs
- At least 1 approval must be from code owner (tech lead)
- Approvals from people in PR (author, committers) don't count
- If PR reviewed, then code changes → need new review

## 5. Configure Automated Security Checks

Every commit must pass:

**Secret scanning:**
```bash
# Block commits with hardcoded secrets
gh secret-scanning enable --org your-org
```

**Dependency scanning:**
```bash
# Block PRs with vulnerable dependencies
gh api repos/your-org/repo/vulnerability-alerts -X PATCH -F enabled=true
```

**Code scanning (SAST):**
```bash
# Runs security scan on every commit
# Tools: Semgrep, SonarQube, GitHub Advanced Security
```

## 6. Configure Staging Branch Rules (Lighter)

Staging branch tests code before production. Protection is lighter but still strict:

```bash
gh api repos/your-org/repo/branches/staging/protection \
  -X PUT \
  -f required_status_checks='{"strict":true,"contexts":["build","test","security-scan"]}' \
  -f required_pull_request_reviews='{"dismissal_restrictions":{},"dismiss_stale_reviews":true,"required_approving_review_count":1}' \
  -f enforce_admins=false \
  -f allow_force_pushes=false \
  -f allow_deletions=false
```

**Difference from main:**
- Only 1 approval required (instead of 2)
- Admins can bypass (for emergency hotfixes)
- Still requires tests, build, security scan

## 7. Configure Development Branch (Minimal)

Development branch is where work happens. Minimal protection:

```bash
gh api repos/your-org/repo/branches/develop/protection \
  -X PUT \
  -f required_status_checks='{"strict":false,"contexts":["build","test"]}' \
  -f enforce_admins=false \
  -f allow_force_pushes=true \
  -f allow_deletions=false
```

**Minimal protection:**
- Build and tests must pass
- Force push allowed (devs can rewrite history)
- No approval required
- Admins can bypass anything

## 8. Set Up Continuous Integration

Define what runs on every commit:

**.github/workflows/ci.yml** (GitHub Actions example):

```yaml
name: CI

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm ci
      - run: npm run build
      - run: npm run test
      - run: npm run lint
      - run: npm run typecheck

  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm run security-scan  # gitleaks, trivy, etc.

  coverage:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm test -- --coverage
      - uses: codecov/codecov-action@v3
```

## 9. Set Up Enforcement Rules

Ensure policies are actually enforced (admins don't skip them):

**Rule: Force push blocked on all branches**

```bash
# Even admins cannot force push
gh api repos/your-org/repo/branches/main/protection \
  -F allow_force_pushes=false
```

**Rule: Stale reviews dismissed on code change**

```bash
# If PR approved, then code changes → approval invalidated
gh api repos/your-org/repo/branches/main/protection/required_pull_request_reviews \
  -F dismiss_stale_reviews=true
```

**Rule: No direct pushes to main/staging**

```bash
# All changes must go through PR (no git push directly to main)
# GitHub enforces this automatically with branch protection
```

## 10. Monitor Compliance

Set up alerts if rules are bypassed:

```bash
# Weekly report: Who bypassed branch protection?
# Set up webhook to log to Slack:

curl -X POST $SLACK_WEBHOOK -d '{
  "text": "🚨 Branch protection bypass detected",
  "blocks": [{
    "type": "section",
    "text": {
      "type": "mrkdwn",
      "text": "User: @user\nRepo: repo\nBranch: main\nTime: [timestamp]"
    }
  }]
}'
```

---

## PR Title & Commit Message Convention

Enforce conventional commits:

```
Commit format: [type]: [description]

Types:
feat:   New feature
fix:    Bug fix
refactor: Code refactor (no behavior change)
perf:   Performance improvement
test:   Test additions
docs:   Documentation
chore:  Tooling, config

Examples:
feat: add payment refund API
fix: handle null user profile gracefully
refactor: simplify authentication middleware
```

Add pre-commit hook to enforce:

**.husky/prepare-commit-msg**:

```bash
#!/bin/bash

# Enforce conventional commits
if ! head -1 "$1" | grep -qE '^(feat|fix|refactor|perf|test|docs|chore):'; then
  echo "❌ Commit message must start with: feat|fix|refactor|perf|test|docs|chore"
  exit 1
fi
```

---

## Customization

**For your organization:**

1. **CI tool:** Replace GitHub Actions with your actual CI (GitLab CI, CircleCI, Jenkins):
   - Adjust workflow syntax
   - Update status check context names
   - Adjust runner configuration

2. **Required checks:** Add your specific checks:
   - SAST scan (Semgrep, SonarQube, GitHub Advanced Security)
   - License compliance (FOSSA, Black Duck)
   - Dependency scanning (Dependabot, Snyk)
   - Performance testing (Lighthouse, custom benchmarks)

3. **Approval counts:** Adjust based on team risk tolerance:
   - Paranoid: 3+ approvals on main
   - Cautious: 2+ approvals (default)
   - Trusting: 1 approval (not recommended)

4. **Slack notifications:** Add to workflows to notify team of CI failures:

```yaml
- name: Notify Slack on failure
  if: failure()
  run: |
    curl -X POST $SLACK_WEBHOOK \
      -d '{"text":"❌ CI failed on ${{ github.ref }}"}'
```

5. **Bypass procedures:** Define when admins can bypass:
   - Security patches: require 1 approval
   - Emergency hotfixes: require VP/Eng approval
   - Never bypass: tests, security scan, build
