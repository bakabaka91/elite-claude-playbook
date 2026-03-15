---
name: multi-service-patterns
description: Design inter-service communication, API contracts, versioning, and event-driven architecture. Use when designing a new service, defining API contracts between services, or refactoring a monolith into microservices.
---

# Multi-Service Patterns Skill

## Purpose

As teams grow, services proliferate. This skill provides patterns for designing services that work together: clear contracts, API versioning, event-driven communication, and error handling across service boundaries.

## API Contract First

Before writing code, write the contract.

### Step 1: Define the API Spec

Use OpenAPI 3.1 (or AsyncAPI for async communication):

```yaml
# openapi.yaml
openapi: 3.1.0
info:
  title: User Service API
  version: 1.0.0
servers:
  - url: https://user-service.internal

paths:
  /api/v1/users/{userId}:
    get:
      summary: Get a user by ID
      parameters:
        - name: userId
          in: path
          required: true
          schema:
            type: string
      responses:
        '200':
          description: User found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
        '404':
          description: User not found
        '500':
          description: Internal server error

components:
  schemas:
    User:
      type: object
      required: [id, email, created_at]
      properties:
        id:
          type: string
          format: uuid
        email:
          type: string
          format: email
        name:
          type: string
        created_at:
          type: string
          format: date-time
```

### Step 2: Get Approval

Before implementing:
1. Share the spec with the consuming team(s)
2. Discuss: "Is this what you need? Any missing fields?"
3. Update based on feedback
4. Commit to the repo — it's now a contract

### Step 3: Implement to Spec

Implement the service exactly as specified. The spec is the source of truth, not the code.

**Rules:**
- IMPORTANT: Never deviate from the contract without re-approval
- IMPORTANT: Breaking changes require a major version bump and deprecation period (see Versioning section)
- Return status codes as specified
- Return JSON schema as specified
- Validate all inputs

---

## API Versioning

Plan for change.

### Versioning Strategy

**URL-based (recommended):**
```
/api/v1/users/...
/api/v2/users/...
```

**Header-based:**
```
GET /api/users/...
Accept-Version: v1
```

**Query parameter:**
```
GET /api/users/...?v=1
```

**Recommendation: Use URL-based.** It's explicit, caches better, and is easier to debug.

### Version Lifecycle

| Stage | Duration | Action |
|-------|----------|--------|
| **v1 — Current** | Ongoing | All new requests use v1 |
| **Announce v2** | — | "v1 deprecated, upgrade by [date]" |
| **v2 — Current + v1 deprecated** | 6 months | Both versions work, but v1 is unsupported |
| **v1 — Sunset** | — | Stop accepting v1 requests, return 410 Gone |

### Deprecation Notice

When releasing v2:
```
Header: Deprecation: true
Header: Sunset: Wed, 30 Sep 2024 23:59:59 GMT
Header: Link: <https://api.example.com/docs/v2-migration>; rel="deprecation"
```

### Example: Adding a Field Without Breaking

**v1 response:**
```json
{
  "id": "123",
  "email": "user@example.com"
}
```

**v2 response (backward-compatible):**
```json
{
  "id": "123",
  "email": "user@example.com",
  "last_login": "2024-03-15T10:30:00Z"
}
```

Clients expecting v1 still get `id` and `email`. New clients get `last_login` too.

### Example: Removing a Field (Breaking Change)

**v1:**
```json
{
  "id": "123",
  "email": "user@example.com",
  "legacy_field": "deprecated" ← Removing this
}
```

**Migration:**
1. Release v1.1: include `deprecation` header warning
2. Keep v1.1 for 6 months
3. Release v2 without `legacy_field`
4. Sunset v1.1 after 6 months

---

## Event-Driven Architecture

For loose coupling between services:

### Domain Events

When something important happens, emit an event:

```typescript
// Event schema (Avro or JSON Schema)
type UserCreatedEvent = {
  event_type: "user.created",
  event_id: string,        // Unique event ID for deduplication
  timestamp: string,       // ISO 8601
  aggregate_id: string,    // User ID
  data: {
    user_id: string,
    email: string,
    name: string,
  }
}

// Emit on a message bus (RabbitMQ, Kafka, AWS SQS, etc.)
await eventBus.publish("user.created", {
  event_type: "user.created",
  event_id: uuid(),
  timestamp: new Date().toISOString(),
  aggregate_id: userId,
  data: { user_id, email, name },
});
```

### Event Subscribers

Other services subscribe and react:

```typescript
// Email Service subscribes to user.created
eventBus.subscribe("user.created", async (event) => {
  const { email, name } = event.data;
  await sendWelcomeEmail(email, name);
});
```

### Rules for Events

- IMPORTANT: Every event must have a unique `event_id` — for deduplication
- IMPORTANT: Events must be immutable — don't update events, emit a new one
- IMPORTANT: Subscribers must be idempotent — processing the same event twice should be safe
- Include timestamp (ISO 8601)
- Include aggregate_id (entity ID like user_id)
- Document the event schema (OpenAPI / AsyncAPI)
- Use semantic versioning for event schema versions

### Event Versioning

When the event schema changes:

**v1 event:**
```json
{
  "event_type": "user.created",
  "event_id": "...",
  "timestamp": "...",
  "aggregate_id": "...",
  "data": { "user_id": "...", "email": "..." }
}
```

**v2 event (add optional field):**
```json
{
  "event_type": "user.created",
  "event_id": "...",
  "timestamp": "...",
  "aggregate_id": "...",
  "data": { "user_id": "...", "email": "...", "name": "..." }
}
```

Subscribers must handle both versions:
```typescript
eventBus.subscribe("user.created", async (event) => {
  const email = event.data.email;
  const name = event.data.name ?? "Unknown"; // Handle missing field
});
```

---

## Service-to-Service Authentication

### Mutual TLS (mTLS)

Services verify each other's identity via certificates:

1. Every service gets a certificate signed by a private CA
2. Service A presents its cert to Service B
3. Service B verifies A's cert (checks it was signed by the CA)
4. Communication established

**Setup (Kubernetes):**
```yaml
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
  name: default
spec:
  mtls:
    mode: STRICT  # All services must use mTLS
```

### Service Tokens

If mTLS isn't available:

```typescript
// At startup, service requests a token
const token = await getServiceToken("checkout-service");

// Use token in API calls
const response = await fetch("https://inventory-service/api/check-stock", {
  headers: {
    Authorization: `Bearer ${token}`,
  },
});
```

**Rules:**
- Tokens expire after 15 minutes
- Tokens are scoped to the calling service (tokens can't be reused across services)
- NEVER log tokens
- Rotate token signing key quarterly

---

## Cross-Service Error Handling

### Failure Modes

**Synchronous (request/response):**
- Downstream service is slow → timeout
- Downstream service is down → 503 Service Unavailable
- Downstream returns error → propagate or handle gracefully

**Asynchronous (events):**
- Subscriber service is down → event queued, retried
- Processing fails → dead-letter queue for manual review

### Timeouts

Always use timeouts:

```typescript
// Set aggressive timeouts — fail fast
const controller = new AbortController();
const timeoutId = setTimeout(() => controller.abort(), 2000); // 2 seconds

try {
  const response = await fetch("https://downstream-service/api/data", {
    signal: controller.signal,
  });
} catch (error) {
  if (error.name === 'AbortError') {
    // Timeout — handle gracefully
    return { error: 'Service unavailable, please retry' };
  }
}
```

### Circuit Breaker

If a downstream service fails repeatedly, stop calling it temporarily:

```typescript
const breaker = new CircuitBreaker({
  threshold: 5,              // Fail 5 times
  timeout: 60000,            // Before stopping for 60s
  volumeThreshold: 10,       // Only consider if 10+ requests made
});

breaker.on('open', () => {
  console.error('Circuit breaker opened — downstream service is unhealthy');
  // Return cached data or error, don't call downstream
});
```

### Retry Strategy

Retry only idempotent operations:

```typescript
async function fetchWithRetry(url, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      const response = await fetch(url, { timeout: 2000 });
      return response;
    } catch (error) {
      if (i === maxRetries - 1) throw error; // Last try, give up

      // Exponential backoff: 1s, 2s, 4s
      const delay = Math.pow(2, i) * 1000;
      await new Promise(resolve => setTimeout(resolve, delay));
    }
  }
}
```

---

## Idempotency Keys

For payment, order, or other state-changing operations, use an idempotency key:

```typescript
// Client generates a unique key per request
const idempotencyKey = uuid();

const response = await fetch("https://payment-service/api/charges", {
  method: 'POST',
  headers: {
    'Idempotency-Key': idempotencyKey,
  },
  body: JSON.stringify({ amount: 1000 }),
});
```

**Server-side:**
```typescript
export async function POST(req: NextRequest) {
  const idempotencyKey = req.headers.get('Idempotency-Key');

  // Check if we've already processed this key
  const existing = await getProcessedRequest(idempotencyKey);
  if (existing) {
    return NextResponse.json(existing.result); // Return cached result
  }

  // Process the request
  const result = await processPayment(...);

  // Store the result keyed by idempotency-key
  await storeProcessedRequest(idempotencyKey, result);

  return NextResponse.json(result);
}
```

---

## Distributed Tracing

When requests span multiple services, trace them:

```typescript
// Service A → Service B → Service C

import { trace } from '@opentelemetry/api';

const tracer = trace.getTracer('checkout-service');
const span = tracer.startSpan('process-order');

span.setAttribute('order_id', orderId);
span.addEvent('order_validation_started');

// Pass trace ID to downstream service
const response = await fetch("https://inventory-service/api/reserve", {
  headers: {
    'traceparent': span.spanContext().traceId,
  },
});

span.addEvent('inventory_reserved');
span.end();
```

---

## Customization

Update this skill with your organisation's context:

- Replace OpenAPI examples with your actual service contracts
- Link to your API documentation (Swagger UI, Redoc)
- Add your message bus tool (Kafka, RabbitMQ, SQS)
- Link to your event schema registry
- Add your CA certificate distribution method
- Link to your distributed tracing tool (Datadog, Jaeger, etc.)
- Add your service-to-service authentication system
