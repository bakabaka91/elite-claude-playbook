---
name: data-classification
description: Classify data by sensitivity level (public, internal, confidential, restricted) and enforce handling rules. Use when designing database schemas, implementing new features, or preparing for compliance audits.
---

# Data Classification Skill

## Purpose

At 50-150 engineers, you process sensitive data (customer payment info, user SSNs, health records, etc.). Without data classification:
- Engineers log SSNs to Sentry (compliance breach)
- Developers cache credit cards in Redis (compliance breach)
- Interns export customer database to personal laptop (security breach)
- You can't answer auditors: "Do you have customer PII?" or "Is it encrypted?"

This skill provides:
1. Data classification tiers (public → internal → confidential → restricted)
2. Handling rules per tier (storage, access, logging, deletion)
3. Compliance mapping (which data is GDPR/HIPAA/PCI-covered?)
4. Detection patterns (how to find sensitive data in code)
5. Audit checklist (are we compliant?)

---

## How to Run

Classify all data your org handles:
1. List all data types (customer names, emails, SSNs, payment cards, health records)
2. Assign tier: public, internal, confidential, restricted
3. Document handling rules: where can it be stored? Who can access? How long to keep?
4. Add to CLAUDE.md: engineering team knows the rules
5. Scan code for violations (grep for patterns)
6. Audit quarterly: are we following the rules?

**Output:** Write to `docs/data-classification.md` with:
1. Data tier definitions and examples
2. Handling rules per tier (table)
3. Compliance obligations (GDPR, HIPAA, PCI-DSS)
4. Detection patterns (what to grep for)
5. Audit checklist (are we compliant?)

---

## Step 1: Define Data Tiers

### Tier 1: PUBLIC

Data that's okay to share with the world.

**Examples:**
- Product names and features
- Blog posts and marketing content
- Anonymized metrics ("5M users worldwide")
- Public API documentation

**Handling rules:**
- **Storage:** No restrictions (can be in public GitHub, S3 bucket, etc.)
- **Access:** Anyone
- **Logging:** Fine to log anywhere
- **Encryption:** Not required
- **Deletion:** No retention requirement
- **Sharing:** Can be shared freely

**Rule:** NEVER classify data as public unless you'd be fine seeing it on Twitter.

---

### Tier 2: INTERNAL

Data for your company only, not sensitive.

**Examples:**
- Employee directory (names, emails, Slack handles)
- Internal metrics and dashboards
- Company financial summaries (not per-customer)
- Design documents and technical specs
- Non-customer usage stats

**Handling rules:**
- **Storage:** Stored in secured systems (GitHub private repo, internal wiki)
- **Access:** Any employee, no special approval
- **Logging:** Fine to log in Sentry/DataDog (internal tools only)
- **Encryption:** At rest if stored in cloud (good practice, not required)
- **Deletion:** Purge when no longer needed (no legal hold)
- **Sharing:** Internal employees only; do not share externally

**Rule:** Assume some employees might leave or get compromised. Encrypt at rest.

---

### Tier 3: CONFIDENTIAL

Sensitive customer or business data. Requires protection.

**Examples:**
- Customer names and email addresses (PII)
- Usage patterns (which features they use)
- Customer company information
- Non-payment billing info (invoices, account status)
- Business intelligence (revenue by region, customer retention)
- API keys, passwords, secrets

**Handling rules:**
- **Storage:** Encrypted at rest (AES-256 minimum)
- **Access:** Specific teams only (finance, customer success, engineering)
- **Logging:** NEVER log to Sentry/DataDog/public logs
  - Example: Don't log "User 12345 from Acme Corp"
  - Log: "User [redacted] accessed account"
- **Encryption:** Both at rest and in transit (TLS)
- **Deletion:** Delete when no longer needed (no indefinite retention)
- **Audit log:** Log who accessed this data and when
- **Sharing:** Requires approval to share externally
- **PII policy:** Use hashing/pseudonymization in development (don't use real customer data in non-prod)

**Rule:** Assume this data could be stolen. Treat as high-value target.

---

### Tier 4: RESTRICTED

Highly sensitive data covered by compliance regulations.

**Examples:**
- Credit card numbers (PCI-DSS)
- SSNs (PCI-DSS, HIPAA, GDPR)
- Health information (HIPAA)
- Biometric data (GDPR)
- Payment card magnetic stripe data
- Bank account numbers
- Authentication tokens (JWT, API keys)
- Passwords (hashed only, never plaintext)

**Handling rules:**
- **Storage:** Encrypted at rest with key management system (AWS KMS, HashiCorp Vault)
- **Access:** Only code that absolutely needs it
  - Example: Payment processor can access credit cards
  - Example: Health service can access health records
  - Counter-example: Analytics should NOT access SSNs
- **Logging:** ABSOLUTELY NEVER log to any log system
  - Example: ❌ "Processing credit card 4532 1111 1111 1111"
  - Example: ✅ "Processing payment" (no card details)
- **Encryption:** At rest AND in transit (TLS + encryption)
- **Deletion:** Delete immediately when no longer needed
  - Credit card: delete after payment completes
  - SSN: delete after identity verification succeeds
  - Health data: delete per HIPAA retention policy
- **Audit log:** Immutable audit trail of who accessed, when, why
- **Testing:** NEVER use real data in development
  - Use tokenized test cards (Stripe test card 4242 4242 4242 4242)
  - Use synthetic SSNs (999-99-9999)
  - Use anonymized health data
- **Backups:** Encrypted backups with restricted access
- **Sharing:** Requires executive approval + legal review

**Rule:** Treat as if your job depends on it. (It might.)

---

## Step 2: Create Data Inventory

List all data your services handle:

| Data Type | Tier | Storage | Owner | Retention |
|-----------|------|---------|-------|-----------|
| Customer name | CONFIDENTIAL | Postgres | User Service | Delete on account closure |
| Email address | CONFIDENTIAL | Postgres | User Service | Delete on account closure |
| Phone number | CONFIDENTIAL | Postgres | User Service | Delete on account closure |
| SSN | RESTRICTED | Postgres (encrypted) | Payment Service | Delete after verification (7 days) |
| Credit card | RESTRICTED | Stripe (tokenized) | Payment Service | Delete after charge (0 days) |
| Health records | RESTRICTED | Postgres (encrypted) | Health Service | Per HIPAA retention policy |
| Password hash | RESTRICTED | Postgres (hashed) | Auth Service | For lifetime of account |
| API keys | RESTRICTED | Vault (encrypted) | Platform | Rotate every 90 days |
| Analytics events | INTERNAL | DataDog | Analytics | Purge after 90 days |
| Error logs | INTERNAL | Sentry | Engineering | Purge after 30 days |

---

## Step 3: Map to Compliance Requirements

Different regulations cover different data:

### PCI-DSS (Payment Card Industry Data Security Standard)

**Covers:** Credit cards, debit cards, payment info

**Restricted data:**
- Full card number
- CVV/CVC code
- Cardholder name
- Expiration date
- Magnetic stripe data

**Rules:**
- NEVER store full card number (use Stripe tokenization instead)
- NEVER log card details
- Encrypt card data in transit (TLS 1.2+)
- Implement access controls (only payment team)
- Quarterly security assessments

**Penalties:** $100K-$300K per incident, loss of payment processing

---

### GDPR (General Data Protection Regulation)

**Covers:** Personal data of EU residents

**Restricted data:**
- Name, email, phone, address (identifiable personal data)
- Cookie IDs, IP addresses
- Location data
- Transaction history
- User preferences

**Rules:**
- Right to deletion: EU residents can request account deletion
  - You have 30 days to delete all their data
  - Exception: legal holds, fraud prevention
- Right to export: EU residents can request download of all their data
- Data processing agreements: if using third parties (Stripe, SendGrid), have DPA signed
- Privacy policy: must disclose what data you collect and why
- No targeted ads based on location without consent

**Penalties:** Up to 4% of global revenue or €20M (whichever is higher)

---

### HIPAA (Health Insurance Portability and Accountability Act)

**Covers:** Protected Health Information (PHI)

**Restricted data:**
- Patient names
- Medical record numbers
- Dates of service
- Diagnoses and treatment plans
- Prescription information
- Insurance information

**Rules:**
- Encryption in transit and at rest
- Access controls: only authorized healthcare workers
- Audit logs: immutable log of all PHI access
- Business Associate Agreement: if using third parties (cloud providers)
- Breach notification: 60-day notification to affected individuals
- Minimum necessary: only collect/access what's needed for treatment

**Penalties:** $100-$50K per violation, $1.5M+ per category per year

---

### SOC 2 (Service Organization Control)

**Covers:** All sensitive data (general audit framework)

**Requirements:**
- Access controls: who can access sensitive data?
- Change management: who can modify sensitive data?
- Encryption: data at rest and in transit
- Logging and monitoring: audit trail of data access
- Disaster recovery: can you restore data if breached?
- Vendor management: are third parties trustworthy?

**Penalties:** Can't work with enterprise customers (they require SOC 2)

---

## Step 4: Define Handling Rules per Tier

Create a matrix of what's allowed:

| Rule | Public | Internal | Confidential | Restricted |
|------|--------|----------|--------------|-----------|
| **Store in Postgres** | ✅ | ✅ | ✅ + encrypt | ✅ + encrypt |
| **Store in Sentry/Logs** | ✅ | ✅ | ❌ (redact) | ❌❌ NEVER |
| **Store in DataDog** | ✅ | ✅ | ❌ (redact) | ❌❌ NEVER |
| **Store in Redis/Cache** | ✅ | ✅ | ⚠️ (short TTL) | ❌❌ NEVER |
| **Store in Slack** | ✅ | ❌ | ❌ | ❌❌ NEVER |
| **Email to team** | ✅ | ⚠️ (BCC only) | ❌ | ❌❌ NEVER |
| **Export to CSV** | ✅ | ❌ | ❌ | ❌❌ NEVER |
| **Backup** | ✅ | ✅ | ✅ + encrypt | ✅ + encrypt |
| **Share with vendor** | ✅ | ❌ | ❌ | ❌ (requires approval) |
| **Use in development** | ✅ | ✅ | ⚠️ (anonymized) | ❌ (synthetic only) |

---

## Step 5: Add Detection Patterns

Train engineers to spot sensitive data:

### Common Violations to Grep For

```bash
# ❌ Credit cards (4-digit BIN visible)
grep -r "4[0-9]\{12\}[0-9]\{3\}" src/
# Example: "4532 1111 1111 1111"

# ❌ SSN (xxx-xx-xxxx pattern)
grep -r "[0-9]\{3\}-[0-9]\{2\}-[0-9]\{4\}" src/
# Example: "123-45-6789"

# ❌ Email in logs
grep -r "email.*@" src/logs.ts
# Example: "Verified user@example.com"

# ❌ API keys hardcoded
grep -r "api_key\|API_KEY\|sk_\|pk_" src/
# Example: "sk_live_51234567890abcdef"

# ❌ Customer names in analytics events
grep -r "firstName\|lastName\|fullName" src/analytics.ts
# Example: "event.track('user_signup', {name: 'John Doe'})"
```

### Pre-Commit Hook to Detect

```bash
#!/bin/bash
# .husky/pre-commit

# Check for credit card patterns
if git diff --cached | grep -qE '[0-9]{4}[- ]?[0-9]{4}[- ]?[0-9]{4}[- ]?[0-9]{4}'; then
  echo "❌ ERROR: Potential credit card found in commit"
  exit 1
fi

# Check for SSN patterns
if git diff --cached | grep -qE '[0-9]{3}-[0-9]{2}-[0-9]{4}'; then
  echo "❌ ERROR: Potential SSN found in commit"
  exit 1
fi

# Check for API keys
if git diff --cached | grep -qE 'sk_|pk_|AKIA'; then
  echo "❌ ERROR: Potential API key found in commit"
  exit 1
fi
```

---

## Step 6: Implement in Code

Add data classification to CLAUDE.md:

```markdown
## Data Handling Policy

This service processes customer data. Classification:

**RESTRICTED data we access:**
- Customer SSN (PCI-DSS, never log, delete after verification)
- Credit cards (PCI-DSS, tokenize via Stripe, never store)

**CONFIDENTIAL data we access:**
- Customer name, email, phone (GDPR, encrypt at rest, delete on closure)
- Usage patterns (internal only, redact in Sentry)

**Internal data we access:**
- Analytics events (purge after 90 days)
- Error logs (redact customer info, purge after 30 days)

**NEVER do:**
- ❌ Log customer SSN or card details
- ❌ Cache RESTRICTED data in Redis
- ❌ Export customer data to CSV
- ❌ Use production data in development
- ❌ Share customer data over email

**ALWAYS do:**
- ✅ Use Stripe for payment processing (PCI-compliant)
- ✅ Encrypt CONFIDENTIAL and RESTRICTED data at rest
- ✅ Redact customer names/emails in Sentry error messages
- ✅ Delete data when retention period expires
- ✅ Audit who accessed RESTRICTED data
```

---

## Step 7: Implement Encryption

### At-Rest Encryption

```
# Postgres: Transparent Data Encryption (TDE) built-in

# AWS RDS: Enable encryption
aws rds modify-db-instance \
  --db-instance-identifier my-database \
  --storage-encrypted

# For sensitive columns, add application-level encryption:
# Encrypt SSN before storing
const encrypted_ssn = await vault.encrypt(user.ssn)
db.users.update({ssn: encrypted_ssn})

# Decrypt when needed
const decrypted_ssn = await vault.decrypt(user.encrypted_ssn)
```

### In-Transit Encryption

```
# Enforce TLS 1.2+ for all traffic

# Nginx config
ssl_protocols TLSv1.2 TLSv1.3;
ssl_ciphers HIGH:!aNULL:!MD5;
ssl_prefer_server_ciphers on;

# API clients
const headers = {
  'Authorization': `Bearer ${token}`,
  // Certificate pinning (optional, for highest security)
}
```

---

## Step 8: Access Control Audit

For RESTRICTED and CONFIDENTIAL data:

```
Question: Who can access SSNs?

Answer: Only Payment Service team (3 engineers)

Audit checklist:
- [ ] SSN field has row-level security (RLS) in database
- [ ] RLS policy: only users with role 'payment_service' can read
- [ ] GitHub access: only payment-service team can view code
- [ ] Quarterly access review: confirm only 3 people still have access
- [ ] Logs: payment_service app is only code that reads SSN column
```

---

## Step 9: Quarterly Data Audit

Every quarter, verify compliance:

```
Q2 Data Audit (June 30):

RESTRICTED Data:
☐ Credit cards: All stored via Stripe (not in our DB) ✅
☐ SSNs: Encrypted in Postgres, rotation via HashiCorp Vault ✅
☐ Access: Only 3 payment engineers can decrypt ✅
☐ Logs: No SSNs logged in Sentry ✅
☐ Backups: Encrypted backups with restricted access ✅

CONFIDENTIAL Data:
☐ Customer emails: Encrypted in Postgres ✅
☐ Audit logs: All access to customer data logged ✅
☐ Deletion: Customers can request deletion, honored in < 7 days ✅
☐ Development: Using anonymized data, not production ✅

Violations found:
❌ 1 engineer exported customer list to CSV (should not be allowed)
   Action: Retrain, add export controls to dashboard

❌ Error logs contained customer names (Sentry redaction not working)
   Action: Fix logging, re-test redaction
```

---

## Step 10: Compliance Readiness Checklist

For each compliance requirement your org has:

### PCI-DSS Readiness (if processing payments)

- [ ] Do NOT store credit card data (use Stripe tokenization)
- [ ] Do NOT log card numbers or CVV
- [ ] Encrypt payment data in transit (TLS 1.2+)
- [ ] Maintain access controls (only payment team)
- [ ] Annual PCI compliance assessment
- [ ] Incident response plan for breaches

### GDPR Readiness (if serving EU users)

- [ ] Privacy policy discloses data collection
- [ ] Users can request data deletion (execute in 30 days)
- [ ] Users can request data export
- [ ] Data processing agreements signed with vendors
- [ ] Breach notification ready (notify users in 72 hours)
- [ ] Default to privacy (collect only necessary data)

### HIPAA Readiness (if handling health records)

- [ ] Business Associate Agreements with all vendors
- [ ] Access controls on PHI (authentication + authorization)
- [ ] Audit logging of all PHI access
- [ ] Encryption at rest and in transit
- [ ] Minimum necessary principle (only collect what needed)
- [ ] Breach notification plan

---

## Customization

**For your organization:**

1. **Adjust tiers for your business:**
   - B2B SaaS: customer usage data is CONFIDENTIAL
   - Healthcare: patient data is RESTRICTED
   - Finance: transaction data is RESTRICTED
   - Adjust tiers based on sensitivity

2. **Add your compliance obligations:**
   - Check: which regulations apply to your business?
   - Add specific handling rules per regulation
   - Document in `docs/data-classification.md`

3. **Implement your encryption:**
   - Use provider defaults (AWS KMS, Vault, etc.)
   - Rotate keys every 90 days
   - Test decryption works (backup/restore test)

4. **Set up your audit logging:**
   - Log all RESTRICTED/CONFIDENTIAL access
   - Query: "Who accessed SSNs last week?"
   - Alert: if unusual access pattern detected

5. **Define your data retention:**
   - Credit cards: delete after charge (0 days)
   - SSN: delete after verification (7 days)
   - Customer data: delete 30 days after account closure
   - Logs: purge after 30 days
   - Adjust per your compliance requirements

6. **Train your team:**
   - Onboarding: data classification policy in week 1
   - Quarterly: compliance refresher
   - On violation: immediate retraining
