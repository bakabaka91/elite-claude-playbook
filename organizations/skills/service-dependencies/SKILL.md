---
name: service-dependencies
description: Map and audit cross-service dependencies, SLO requirements, failure modes, and blast radius. Use when designing new services, refactoring monoliths to microservices, or understanding why a service going down breaks everything.
---

# Service Dependencies Skill

## Purpose

As you scale from 50→150 engineers, services proliferate. Without explicit dependency mapping, you get "Why is the entire system down?" incidents where you don't understand why.

This skill provides:
1. Service dependency matrix (which services depend on which)
2. SLO contracts (if Service A depends on Service B, what SLO does B need?)
3. Failure modes (what happens when Service B goes down?)
4. Blast radius (how many users affected?)
5. Circuit breaker patterns (graceful degradation)

---

## How to Run

Map all services. For each service, answer:
- What does this service depend on?
- What SLO do those dependencies need?
- What happens if a dependency fails?
- How many downstream services are affected?

**Output:** Write to `docs/service-dependencies.md` with:
1. Service dependency matrix (table or graph)
2. SLO contracts per dependency
3. Failure modes and mitigation
4. Blast radius analysis
5. Critical path analysis (services that must never fail)

---

## Step 1: List All Services

Document every service in your org:

| Service | Team | Language | Purpose | Criticality |
|---------|------|----------|---------|-------------|
| User Service | Backend | Go | User auth, profile, permissions | CRITICAL |
| Payment Service | Billing | Python | Process payments, refunds | CRITICAL |
| Order Service | Fulfillment | TypeScript | Create, track, ship orders | CRITICAL |
| Search Service | Platform | Rust | Full-text search | HIGH |
| Email Service | Growth | Node.js | Send transactional emails | MEDIUM |
| Analytics | Data | Python | Event tracking, dashboards | LOW |

---

## Step 2: Map Upstream Dependencies

For each service, list what it depends on:

### Service: Payment Service (Billing Team)

**Upstream dependencies (services that call Payment):**
- Order Service (calls: `POST /charge`)
- Subscription Service (calls: `POST /charge-recurring`)
- Dashboard (calls: `GET /invoices`)

**Downstream dependencies (services Payment calls):**
- User Service (calls: `GET /users/{id}` to validate user)
- Database (Postgres)
- Stripe API (external)

**External APIs:**
- Stripe (payment processor)
- SendGrid (for payment receipts)

---

## Step 3: Define SLO Contracts

For each dependency, define what SLO it must meet:

### Payment Service Dependencies

| Dependency | SLO Requirement | Reason | Consequence if missed |
|------------|-----------------|--------|----------------------|
| User Service | 99.9% availability | Payment processing depends on user validation | Payment API returns 500s |
| Database (Postgres) | 99.99% availability | Transactions must be durable | Data loss, customer refund chaos |
| Stripe API | 99.5% availability (SLA) | External dependency, not our control | Graceful degradation: queue charge for retry |

**Interpretation:**
- If User Service is < 99.9% available, Payment SLA may degrade
- If database is down, Payment must fail safely (not process charges twice)
- If Stripe is down, queue charge and retry (don't lose money)

---

## Step 4: Document Failure Modes

For each dependency, ask: "What happens if this goes down?"

### Failure Mode: User Service Down

**Trigger:** User Service is completely offline (returns 500s)

**Impact on Payment Service:**
```
Payment API called: POST /charge {user_id: 123, amount: 100}

Without mitigation:
1. Payment calls User Service to validate user
2. User Service is down → timeout or 500 error
3. Payment returns 500 to Order Service
4. Order fails to charge customer
5. Customer gets "Payment failed" error (bad UX)
6. Revenue loss: customers can't check out

With mitigation (circuit breaker):
1. Payment calls User Service
2. User Service is down → 3rd request fails → open circuit breaker
3. Payment fails fast (after 10ms, not 30s timeout)
4. Payment logs event: "User Service circuit open, skipping validation"
5. Payment processes charge anyway (accept slight risk)
6. Order completes successfully
7. Customer can checkout (good UX)
8. Async: validate user later when User Service recovers
```

**Mitigation:**
- Circuit breaker on User Service (open after 3 failures)
- Cache user data in Payment Service (valid for 1 hour)
- If User Service down > 5 min, page User Service team
- Fallback: allow payment without validation (accept risk for UX)

---

### Failure Mode: Database Connection Pool Exhausted

**Trigger:** Too many queries exhaust connection pool (typical: 100 connections)

**Impact:**
```
New queries queue up waiting for connection.
Queries timeout after 30 seconds.
API returns 500.
Cascade: Payment Service fails → Order fails → checkout broken.
```

**Mitigation:**
- Connection pool size: 50 connections (not max 100)
- Queries per second limit: auto-reject if > 1000 QPS
- Monitor pool usage: alert at > 80% utilization
- Read replicas for reporting queries (don't exhaust main pool)

---

### Failure Mode: Stripe API Down

**Trigger:** Stripe is completely offline (rare, but happens ~2x/year)

**Impact:**
```
Payment Service tries to charge with Stripe.
Stripe API returns 503.
Payment Service returns 500 to Order Service.
Customer can't checkout.
```

**Mitigation:**
- Don't fail immediately: retry with exponential backoff (1s, 2s, 4s, 8s...)
- After 3 failed attempts (15 seconds total): queue charge for async retry
- Customer sees: "Payment is processing, we'll confirm in 5 minutes"
- Background job: retry charge every 5 minutes for 24 hours
- If still failing after 24 hours: notify customer + payment team
- **Result:** Customers can checkout even if Stripe is down (charge completes 1-24 hours later)

---

## Step 5: Map Blast Radius

For each service, answer: "If this service goes down, how many downstream services break?"

### Service Dependency Graph

```
User Service (CRITICAL)
├── Payment Service (depends on User)
│   ├── Order Service (depends on Payment)
│   └── Subscription Service
├── Dashboard (depends on User)
└── Email Service (might enrich user data)

If User Service down:
→ Payment breaks → Order breaks (2 downstream)
→ Dashboard breaks (1 downstream)
→ Email might fail (1 downstream)
Total blast radius: 4 services affected
Estimated users affected: 100%
Severity: P0 (entire system unusable)
```

### Create Blast Radius Matrix

| Service | If Down, Affects | Severity | MTTR Target |
|---------|------------------|----------|-------------|
| User Service | Payment, Order, Dashboard | P0 | 15 min |
| Payment Service | Order, Subscription | P1 | 1 hour |
| Order Service | (standalone) | P1 | 4 hours |
| Search Service | (standalone) | P2 | 24 hours |
| Analytics | (non-critical) | P3 | N/A |

**Interpretation:**
- User Service is CRITICAL — if down, entire system fails
- Payment Service is HIGH — if down, revenue stops
- Search is MEDIUM — if down, users can't search but can still browse
- Analytics is LOW — if down, no impact to users

---

## Step 6: Define Critical Path

The critical path is services that must never fail:

```
Critical path:
1. User Service (auth, permissions)
2. Payment Service (charge customers)
3. Database (store state)

If ANY of these fail:
→ Revenue stops
→ Customers can't login
→ Data is at risk

Therefore: these get highest SLO (99.99%), highest alerting, most on-call attention
```

---

## Step 7: Dependency Audit Checklist

For each dependency, verify:

**Circuit Breaker / Timeout:**
- [ ] Has timeout (not infinite)
- [ ] Has circuit breaker (fails fast after N failures)
- [ ] Timeout < 10 seconds (don't cascade timeout)
- [ ] Circuit breaker threshold: fail after 3-5 attempts

**Retry Logic:**
- [ ] Retries with exponential backoff (1s, 2s, 4s, 8s, 16s...)
- [ ] Max retries: 3-5 attempts
- [ ] Retries idempotent (safe to retry, won't double-charge)
- [ ] Dead letter queue for failed retries (manual review)

**Monitoring:**
- [ ] Alert if dependency latency > 150% of baseline
- [ ] Alert if dependency error rate > 1% (above baseline)
- [ ] Alert if dependency unavailable > 1 minute
- [ ] Dashboard shows dependency health

**Fallback:**
- [ ] Has graceful degradation (if dependency down, degrade feature not break service)
- [ ] Example: if Search Service down, show fallback (no search) but site still works
- [ ] Example: if Cache down, query database slower but still works

**SLO Contract:**
- [ ] Documented in CLAUDE.md
- [ ] Dependency team aware of SLO requirement
- [ ] If dependency misses SLO, escalation procedure defined
- [ ] Example: if User Service < 99.9%, Payment team and User team meet to fix

---

## Step 8: Async Communication Pattern

For non-critical dependencies, use async (events) instead of sync (API calls):

**Sync pattern (tight coupling):**
```
Order Service calls Payment Service (sync).
If Payment down → Order fails.
High coupling = high failure risk.
```

**Async pattern (loose coupling):**
```
Order Service publishes event: "order.created"
Payment Service subscribes: listens for "order.created"
Payment processes charge asynchronously.
If Payment down, Order doesn't fail (event is queued).
Low coupling = resilient.
```

**When to use Async:**
- Secondary operations (email, analytics, notifications)
- Non-blocking operations (don't need result immediately)
- Examples: order.created → send email, log to analytics

**When to use Sync:**
- Critical operations (need result immediately)
- Validation (must validate before proceeding)
- Examples: validate user before charging, validate inventory before creating order

---

## Step 9: Multi-Region / Multi-AZ Awareness

If services are in multiple regions:

```
Payment Service in US-East (primary)
Payment Service in US-West (backup)

Order Service in US-East calls Payment-US-East
If US-East Payment down → failover to Payment-US-West?
Or reject order and notify customer?

Define: should failover be automatic or manual?
```

Add to dependency map:
```
Payment Service (US-East) ← primary
Payment Service (US-West) ← backup, async replica

Order Service logic:
1. Try Payment-US-East (timeout: 5s)
2. If fails, try Payment-US-West (timeout: 5s)
3. If both fail, queue order for retry (async)
```

---

## Step 10: Communication & Training

Share dependency map with team:

```
Post to #engineering Slack:
"📊 Service dependency map is now at docs/service-dependencies.md

Key insights:
- User Service is CRITICAL (99.99% SLO)
- Payment depends on User (if User down, can't charge)
- If Search down, site still works (async, non-critical)
- All services have circuit breakers to fail fast

Review and ask questions in #architecture"
```

Train teams:
- "Your service depends on User Service with 99.9% SLO"
- "If User Service down, use cached data (circuit breaker opens)"
- "If your SLO is missed, we'll have a postmortem"

---

## Customization

**For your organization:**

1. **Add your services:**
   - Replace example services with your actual services
   - Update team ownership
   - Update languages and frameworks

2. **Define your SLOs:**
   - User Service: 99.9%? 99.99%?
   - Payment Service: 99.95%?
   - Non-critical: 99%?
   - Adjust based on revenue impact

3. **Add your monitoring:**
   - Replace DataDog with your APM tool
   - Replace Prometheus with your metrics tool
   - Define alert thresholds for your SLOs

4. **Add your communication:**
   - Replace Slack channels with your team channels
   - Define who to page on-call if SLO missed
   - Define escalation: 1st → tech lead → VP/Eng

5. **Document your fallbacks:**
   - If Search down, show browsing (not broken search)
   - If Analytics down, site still works
   - If Email down, queue for retry (don't lose emails)

6. **Add to CLAUDE.md:**
   - Link each service's CLAUDE.md to `docs/service-dependencies.md`
   - Note: "This service depends on X (99.9% SLO), see docs/service-dependencies.md"
   - Note: "This service is depended on by Y, Z (must maintain 99.9% SLO)"
