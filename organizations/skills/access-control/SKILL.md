---
name: access-control
description: Implement least-privilege IAM, RBAC patterns, and access review procedures. Use when designing authentication/authorisation, reviewing permissions, auditing access, or onboarding a new engineer.
---

# Access Control Skill

## Purpose

Access control is where security breaks down at scale. This skill provides patterns for implementing least-privilege IAM, role-based access control (RBAC), and regular access reviews.

## RBAC Pattern Scaffold

Every service needs clear roles and permissions. Build RBAC like this:

### Step 1: Define Roles

Identify the distinct user types or organisational units:

```typescript
enum Role {
  ADMIN = "admin",           // Can do everything
  TEAM_LEAD = "team_lead",   // Can manage their team, view analytics
  ENGINEER = "engineer",     // Can view own work and team work
  VIEWER = "viewer",         // Read-only access
  GUEST = "guest",           // Time-limited external access
}
```

Do not create role-per-user. Roles should map to job functions, not individuals.

### Step 2: Define Permissions

List specific capabilities:

```typescript
enum Permission {
  // Viewing permissions
  VIEW_ORGANISATION = "view_organisation",
  VIEW_TEAM = "view_team",
  VIEW_ANALYTICS = "view_analytics",

  // Modification permissions
  EDIT_TEAM_SETTINGS = "edit_team_settings",
  DELETE_RESOURCE = "delete_resource",

  // Access control
  MANAGE_MEMBERS = "manage_members",
  MANAGE_ROLES = "manage_roles",
}
```

### Step 3: Map Roles to Permissions

Create a permission matrix:

| Role | VIEW_ORGANISATION | VIEW_TEAM | EDIT_TEAM | MANAGE_MEMBERS | MANAGE_ROLES |
|------|-------------------|-----------|-----------|----------------|--------------|
| ADMIN | ✓ | ✓ | ✓ | ✓ | ✓ |
| TEAM_LEAD | ✓ | ✓ (own) | ✓ (own) | ✓ (own) | ✗ |
| ENGINEER | ✗ | ✓ (own) | ✗ | ✗ | ✗ |
| VIEWER | ✓ | ✓ | ✗ | ✗ | ✗ |
| GUEST | ✗ | ✓ (limited) | ✗ | ✗ | ✗ |

### Step 4: Implement Permission Checks

**Server-side always.** Never trust client-side role checks.

```typescript
async function canUserEditTeam(userId: string, teamId: string): Promise<boolean> {
  const user = await getUser(userId);
  if (!user) return false;

  // ADMIN can edit any team
  if (user.role === Role.ADMIN) return true;

  // TEAM_LEAD can edit their own team
  if (user.role === Role.TEAM_LEAD) {
    const isOwner = user.team_id === teamId;
    return isOwner;
  }

  // Others cannot edit
  return false;
}

// Usage in API route
export async function PUT(req: NextRequest) {
  const user = await getAuthenticatedUser(req);
  const { teamId } = await req.json();

  if (!await canUserEditTeam(user.id, teamId)) {
    return NextResponse.json({ error: 'Forbidden' }, { status: 403 });
  }

  // Proceed with edit
}
```

### Step 5: Implement Row-Level Security (RLS)

For database-backed access, use RLS policies (Supabase / Postgres):

```sql
-- Only users on the same team can view team_members
CREATE POLICY team_view_own_members
ON team_members
FOR SELECT
USING (team_id IN (
  SELECT team_id FROM users WHERE id = auth.uid()
));

-- Team leads can edit their team's members
CREATE POLICY team_lead_edit_members
ON team_members
FOR UPDATE
USING (
  team_id IN (
    SELECT team_id FROM users
    WHERE id = auth.uid() AND role = 'team_lead'
  )
);
```

---

## Service-to-Service Authentication

For internal services calling each other:

### Mutual TLS (mTLS)

- All internal services have a certificate signed by a private CA
- Service A presents its certificate to Service B
- Service B verifies A's certificate before accepting requests
- Prevents rogue services from calling each other

### Service Tokens

If mTLS is not available:

```typescript
// At startup, Service A requests a token from the auth service
const token = await getServiceToken("service-a");

// Service A uses token in Authorization header
const response = await fetch("https://service-b/api/data", {
  headers: {
    Authorization: `Bearer ${token}`,
  },
});

// Service B validates token with auth service
const isValid = await validateServiceToken(token);
```

**Rules:**
- IMPORTANT: Service tokens must have an expiration (15 minutes recommended)
- IMPORTANT: Service tokens must be scoped to specific services (Service A cannot use Service B's token)
- NEVER log service tokens
- Rotate service tokens quarterly

---

## API Key Management

For external integrations:

1. **Generate keys** with a unique prefix:
   ```
   api_key = "sk_live_" + secureRandom(64)
   ```

2. **Hash keys before storage** (SHA-256):
   ```typescript
   const hash = crypto.createHash('sha256').update(apiKey).digest('hex');
   // Store hash in DB, not the key itself
   ```

3. **Rotate quarterly:**
   - Send email to API key owner: "Your API key expires in 30 days"
   - After 30 days, disable old key
   - Support 30-day overlap period so integrations can switch

4. **Scope keys to permissions:**
   ```typescript
   const key = {
     prefix: "sk_live_",
     hash: "...",
     scopes: ["read:analytics", "write:webhooks"],
     expires_at: "2024-06-15",
     last_used: "2024-03-10",
   };
   ```

---

## Least-Privilege Audit Checklist

Run this quarterly:

- [ ] **User access review:** Do all active users still need their access? (Remove departed employees within 24 hours)
- [ ] **Dormant accounts:** Disable or delete accounts inactive for 90+ days
- [ ] **API key audit:** Are unused API keys still active? (Delete unused keys)
- [ ] **Service tokens:** Have all service tokens been rotated in the last 90 days?
- [ ] **Role appropriateness:** Does each user's role match their actual job function?
- [ ] **Group membership:** For team-based RBAC, has group membership changed since last review?
- [ ] **OAuth scope creep:** Are connected apps still requesting only the scopes they need?
- [ ] **Database credentials:** Are there any hardcoded database passwords? (Audit logs will show if so)
- [ ] **SSH/API access:** Are there SSH keys or long-lived tokens that haven't been used in 90 days?

**Document the review:**
```markdown
# Access Review — [Date]

## Users Reviewed: [N]
- Removed: [Names/counts]
- Disabled: [Names/counts]
- Role change: [Names]

## API Keys Reviewed: [N]
- Deleted: [N]
- Rotated: [N]

## Service Tokens
- Rotated: [N]
- Expired and removed: [N]

## Findings
- [Notable finding 1]
- [Notable finding 2]

## Next Review: [Date, 90 days from now]
```

---

## Session Token Hygiene

For user authentication (login):

1. **Use HTTP-only cookies** — not localStorage or sessionStorage
   ```typescript
   res.setHeader('Set-Cookie', `session=${token}; HttpOnly; Secure; SameSite=Strict`);
   ```

2. **Token expiration:**
   - Short-lived access tokens: 15 minutes
   - Refresh tokens: 30 days, HttpOnly only
   - Auto-refresh: renew access token when 5 minutes remain

3. **Invalidate on logout:**
   ```typescript
   // In database, mark token as revoked
   await revokeSessionToken(sessionToken);
   ```

4. **Tie tokens to user's user-agent or IP:**
   - Store `user_agent` and `ip_address` in token record
   - On each request, verify they match
   - Mismatch = potential hijacking, deny access

---

## MFA Enforcement

For sensitive operations:

1. **Require MFA for:**
   - GitHub / cloud provider access (mandatory)
   - Admin/superuser accounts (mandatory)
   - Access to production infrastructure (mandatory)
   - Viewing API keys or secrets (optional but recommended)

2. **MFA methods to support:**
   - TOTP (Time-based One-Time Password) — Google Authenticator, Authy
   - Backup codes — printed and stored securely
   - WebAuthn / FIDO2 — hardware keys (YubiKey, etc.)
   - SMS — last resort (SIM swap risk)

3. **NEVER fall back to email for MFA** — use TOTP or backup codes

---

## Zero Trust Architecture

Design as if every request is untrusted:

1. **Authenticate every request** — even internal API calls
2. **Authorise every operation** — check permissions server-side
3. **Encrypt in transit** — HTTPS only, no HTTP
4. **Encrypt at rest** — sensitive data encrypted in database
5. **Log access** — audit trail of who accessed what and when
6. **Monitor anomalies** — flag unusual access patterns (bulk downloads, off-hours, new IP, etc.)

---

## Customization

Update this skill with your organisation's context:

- Replace example roles with your org's actual roles
- Link to your permission matrix (or create one in Notion/Confluence)
- Replace TOTP with your MFA provider (Okta, Auth0, AWS Cognito)
- Add your database name and schema for RLS examples
- Link to your internal CA for mTLS setup instructions
- Add your internal service token endpoint
