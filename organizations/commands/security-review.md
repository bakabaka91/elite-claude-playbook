Run a security pre-merge checklist on the current branch. This is for sensitive changes (auth, payments, access control, database schema).

1. **Scan for secrets** in changed files:
   - Search for: AWS keys, GitHub tokens, API keys, private keys, database passwords
   - Run: `git diff main -- | gitleaks detect --verbose`
   - Any secrets found = BLOCKED, commit a fix before re-running

2. **Verify authentication** on all new API routes:
   - Does every protected endpoint have an auth check?
   - Is there a public endpoint? (health checks only, document why)
   - Check: `if (!user) return 401` at the start of every handler

3. **Verify input validation** on all new API routes:
   - Does every endpoint validate request body, query params, path params?
   - Check: uses Zod, Joi, or similar schema validator
   - Check: bad input returns 400, not 500

4. **Verify database queries** are safe:
   - Are all queries parameterised / using ORM? (never string concatenation)
   - Is RLS enabled on any new tables?
   - Check: `CREATE POLICY ... ON table FOR SELECT USING (...)`
   - Check: migrations are idempotent (can run twice safely)

5. **Verify new tables have RLS**:
   - Check: `SELECT tablename FROM pg_tables WHERE schemaname = 'public'`
   - Check: `SELECT * FROM pg_policies WHERE tablename = 'new_table'`
   - If no policies exist = BLOCKED

6. **Verify new environment variables** are documented:
   - Check: `.env.example` updated with new vars
   - Check: vars are not hardcoded in code
   - Check: secret vars are explained as "Sensitive — use vault"

7. **Scan for PII logging**:
   - Search changed code for `console.log` or `.log()` calls
   - Check: no logging of user emails, phone numbers, SSNs, payment info
   - Check: logging of user IDs and anonymous data only

8. **Check for common vulnerabilities:**
   - [ ] No `eval()` or dynamic code execution
   - [ ] No SQL injection (parameterised queries only)
   - [ ] No XSS (user input escaped, React context)
   - [ ] No CSRF (SameSite cookies, CSRF tokens if needed)
   - [ ] No rate limiting bypass (throttle endpoints appropriately)
   - [ ] No hardcoded IPs or domains

**Report result:**

```
APPROVED — All checks passed
OR
BLOCKED — Issues found:
- [Issue 1 and how to fix]
- [Issue 2 and how to fix]

Fix and re-request review.
```

Do not merge until all security checks pass.
