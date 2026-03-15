---
name: dependency-governance
description: Manage open-source dependencies, enforce license compliance, and patch vulnerabilities on schedule. Use when evaluating new packages, updating dependencies, or conducting a dependency audit.
---

# Dependency Governance Skill

## Purpose

Dependencies are supply chain risk. This skill provides a framework for managing open-source packages: licensing, vulnerability response, and update workflow.

## License Compliance

Classify licenses into tiers. Commercial products cannot use GPL without open-sourcing.

### License Classification

| Tier | Licenses | Usage | Review Needed |
|------|----------|-------|---------------|
| **Allowed** | MIT, Apache 2.0, BSD, ISC, MPL 2.0 | Use freely | No |
| **Conditional** | LGPL, EPL, CDDL | Use in standalone components, not core product | Yes — legal review |
| **Restricted** | GPL v2/v3, AGPL | Cannot use in commercial code without open-sourcing | **Blocked unless approved** |
| **Proprietary** | Custom licenses, no license | Vendor agreement required | **Blocked — legal + procurement** |

### License Check Workflow

**Before adding a new dependency:**

```bash
# Show the license of this package and its transitive deps
npm ls --depth=0 | grep -i license
# Or use a tool
npx license-checker --onlyAllow "MIT,Apache-2.0,BSD" --production
```

**If adding a Conditional or Restricted package:**

1. Open a JIRA ticket: `[LEGAL] Evaluate license for [package]`
2. Provide to legal/compliance team:
   - Package name and version
   - License text
   - How many lines of your code use it?
   - Is this core product or a utility?
3. Do not merge the PR until legal approves

**Set up CI to block unapproved licenses:**

```yaml
# .github/workflows/license-check.yml
name: License Check
on: [pull_request]
jobs:
  license-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - run: npm ci
      - run: npx license-checker --failOn GPL --onlyAllow "MIT,Apache-2.0,BSD,ISC,Apache-2.0"
```

---

## Vulnerability Management

Patches exist on a schedule. Organisations with compliance requirements (SOC 2, HIPAA) must patch within SLAs.

### Vulnerability Severity & SLA

| Severity | CVSS Score | Definition | SLA |
|----------|-----------|-----------|-----|
| **Critical** | 9.0-10.0 | Exploitable remotely without auth | **24 hours** |
| **High** | 7.0-8.9 | Exploitable remotely or locally without auth | **7 days** |
| **Medium** | 4.0-6.9 | Limited impact or requires specific conditions | **30 days** |
| **Low** | 0.1-3.9 | Minimal impact | **No SLA** (next release) |

### Patch Workflow

**Detection:**
```bash
# Run audit on every PR
npm audit --production

# Or in CI, block PRs with Critical vulns
npm audit --audit-level=critical
```

**Reporting:**
```markdown
# Vulnerability Found: [Package] [CVE]
- Severity: [Critical/High/Medium/Low]
- Affected versions: [X.Y.Z]
- Exploit complexity: [Low/High]
- Data at risk: [Yes/No, describe]

## Action Plan
- [ ] Patch available? If yes, version: [X.Y.Z+patch]
- [ ] Schedule patch PR for: [Date within SLA]
- [ ] If no patch: mitigate by [describe workaround or vendoring]
```

**Patching:**
```bash
# Pin to vulnerable version first (shows in lock file)
npm install package@^X.Y.Z

# Or use Dependabot/Renovate to auto-create PR
# Review and merge
npm ci
npm audit # Should show issue resolved
```

**Track in JIRA:**
```
[SEC] Patch [package] CVE-XXXX-XXXXX
- Status: [Open / In Review / Deployed]
- Due: [SLA date]
- Deployed to: [dev / staging / production]
```

---

## Dependency Update Policy

Keep dependencies current — old versions have accumulated vulnerabilities.

### Update Strategy

**Tier 1 — Security Updates (immediate):**
- Patch releases that fix vulnerabilities
- Examples: lodash 4.17.20 → 4.17.21
- Merge without extensive testing if lock file alone changed

**Tier 2 — Minor Updates (monthly):**
- Feature releases that are backward-compatible
- Examples: Next.js 14.0.0 → 14.1.0
- Test locally, run CI, merge if passing
- Schedule monthly: first Monday of each month

**Tier 3 — Major Updates (quarterly):**
- Breaking changes, major version bumps
- Examples: React 18 → 19, Next.js 13 → 14
- Allocate sprint capacity
- Test thoroughly on staging
- Coordinate with team leads

### Use Dependabot or Renovate

**Setup (Dependabot, via GitHub):**

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 5
    reviewers:
      - "devops-team"
    allow:
      - dependency-type: "all"
```

**Renovate alternative (more flexible):**

```json
// renovate.json
{
  "extends": ["config:base"],
  "packageRules": [
    {
      "groupName": "Security updates",
      "matchUpdateTypes": ["patch"],
      "schedule": ["at any time"]
    },
    {
      "groupName": "Minor updates",
      "matchUpdateTypes": ["minor"],
      "schedule": ["before 3am on Monday"]
    },
    {
      "groupName": "Major updates",
      "matchUpdateTypes": ["major"],
      "assignees": ["@devops-team"],
      "schedule": ["before 3am on the first Monday of the month"]
    }
  ]
}
```

---

## Lock File Integrity

Lock files (`package-lock.json`, `yarn.lock`, `poetry.lock`) are your source of truth.

### Rules

- **IMPORTANT: Lock files must be committed.** Never `.gitignore` them.
- **IMPORTANT: Commit lock file changes only when intentionally updating dependencies.**
- **NEVER modify lock files by hand.** Always use `npm install`, `yarn add`, etc.

**Verify lock file integrity in CI:**

```bash
# For npm: ensure node_modules matches lock file
npm ci # NOT npm install

# For Python: same
pip install --require-hashes -r requirements.txt

# Checksum lock file
sha256sum package-lock.json > package-lock.sha256
git add package-lock.sha256
```

**On PR creation, verify lock file wasn't accidentally modified:**

```yaml
# .github/workflows/lockfile-check.yml
- name: Check lock file
  run: |
    npm ci --frozen-lockfile
    # If this fails, lock file is out of sync
```

---

## Internal Package Registry

For internal packages (shared libraries):

1. **Use a private registry:**
   - npm: GitHub Packages, Verdaccio, or npm.org private
   - Python: GitHub Packages or private PyPI
   - Java: GitHub Packages or Artifactory

2. **Semantic versioning mandatory:**
   ```
   MAJOR.MINOR.PATCH
   1.2.3 → 2.0.0 = breaking change
   1.2.3 → 1.3.0 = new feature, backward-compatible
   1.2.3 → 1.2.4 = bug fix, backward-compatible
   ```

3. **Changelog required for every release:**
   ```markdown
   # Version 2.0.0

   ## Breaking Changes
   - Removed deprecated `oldFunction()` — use `newFunction()` instead

   ## New Features
   - Added TypeScript types for all exports

   ## Bug Fixes
   - Fixed race condition in `asyncOperation()`

   ## Migration Guide
   See [upgrade-guide.md](upgrade-guide.md)
   ```

4. **Deprecation period for major versions:**
   - Release v2.0.0
   - Keep v1.x in support for 6 months
   - Email consumers: "v1.x deprecated, migrate to v2.0.0 by [date]"
   - After 6 months, unpublish v1.x

---

## Dependency Audit Checklist

Run quarterly:

- [ ] **License audit:** `npx license-checker` — any GPL or proprietary? (Should be none)
- [ ] **Vulnerability scan:** `npm audit` — critical or high vulns? (Should be zero)
- [ ] **Outdated:** `npm outdated` — which packages are out of date?
- [ ] **Unused:** `npm ls --depth=0` — are all listed dependencies actually used?
- [ ] **Transitive deps:** `npm ls` — do transitive dependencies introduce risk?
- [ ] **Lock file drift:** Does lock file match actual node_modules state?
- [ ] **Deprecation notices:** Are any dependencies showing deprecation warnings?
- [ ] **CVE notifications:** Search `cves.json` for any known CVEs in your deps

**Report:**
```markdown
# Dependency Audit — [Date]

## Vulnerabilities
- Critical: 0
- High: 2 (scheduled for patch on [dates])
- Medium: 5 (scheduled for patch on [dates])

## License Issues
- GPL: 0
- Proprietary: 0 (good!)

## Updates Needed
- Major: [N] (scheduled for [date])
- Minor: [N] (auto-updated)
- Patch: [N] (auto-updated)

## Removed
- [Old unused package]

## Blocker Issues
- None

## Next Audit: [90 days from now]
```

---

## Customization

Update this skill with your organisation's context:

- Replace license tiers with your org's legal policy
- Add your compliance requirements (SOC 2, HIPAA, PCI-DSS) and their SLAs
- Link to your internal package registry (GitHub Packages, etc.)
- Link to your JIRA board or issue tracker for vulnerability tracking
- Add your internal deprecation period for major versions
- Link to your approved dependency list (if you have one)
