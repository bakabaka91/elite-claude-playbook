---
name: api-patterns
description: Scaffold and review API routes following project conventions. Use when creating new API endpoints, modifying existing routes, or reviewing API code for correctness.
---

# API Route Patterns

## Before Creating a New Route

1. Check existing routes in your API directory for established patterns
2. Identify which auth method is needed (session-based, bearer token, API key)
3. Determine the validation schema needed for the request body

## Standard Route Structure

Every API route should follow this pattern:

```
1. Authenticate — verify the user/caller
2. Validate — parse and validate request body with schema
3. Business Logic — do the actual work
4. Response — return structured response with proper status code
```

Example (Next.js App Router):

```typescript
import { NextRequest, NextResponse } from 'next/server';
import { z } from 'zod';

const requestSchema = z.object({
  // define your fields
});

export async function POST(req: NextRequest) {
  try {
    // 1. Authenticate
    const user = await getAuthenticatedUser(req);
    if (!user) {
      return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
    }

    // 2. Validate
    const body = await req.json();
    const parsed = requestSchema.safeParse(body);
    if (!parsed.success) {
      return NextResponse.json(
        { error: 'Invalid request', details: parsed.error.flatten() },
        { status: 400 }
      );
    }

    // 3. Business logic
    const result = await performAction(parsed.data, user.id);

    // 4. Response
    return NextResponse.json(result, { status: 200 });
  } catch (error) {
    console.error('[ROUTE_NAME] Error:', error);
    return NextResponse.json({ error: 'Internal server error' }, { status: 500 });
  }
}
```

## Rules

- IMPORTANT: Every route must authenticate the user — no unauthenticated endpoints except health checks
- IMPORTANT: Every route must validate input with a schema (Zod, Joi, etc.) — never trust request body
- IMPORTANT: All database queries go through your ORM — never write raw SQL
- Use proper HTTP status codes: 200 (success), 201 (created), 400 (bad input), 401 (unauthorized), 404 (not found), 429 (rate limited), 500 (server error)
- Log errors with context: `[ROUTE_NAME] Error: ...`
- NEVER log PII (email addresses, names, phone numbers) to server logs
- Add rate limiting for public-facing or expensive endpoints (AI calls, auth attempts)
- If your project uses event sourcing: state mutations must create events

## Customization

Update this skill with your project-specific details:
- Replace the example with your actual auth helper
- Add your ORM import pattern
- Add any project-specific middleware or patterns
- Specify your validation library
