---
name: migration-guide
description: Create and manage database migrations safely. Use when adding tables, columns, indexes, RLS policies, or making any database schema change.
---

# Database Migration Guide

## Before Creating a Migration

1. Check existing migrations in your migrations directory to understand current schema
2. Check your ORM schema files (Drizzle, Prisma, etc.) for TypeScript definitions
3. If a schema reference doc exists, read it for the full picture
4. Plan the migration — what tables/columns/indexes are being added or modified?

## Creating a New Migration

### Step 1: Create the migration file

Use your migration tool to create the file:

```bash
# Drizzle (hand-written SQL)
# Create next sequential file in your migrations directory
# e.g., migrations/00XX_description.sql

# Prisma
npx prisma migrate dev --name description_here

# Supabase CLI
npx supabase migration new description_here
```

### Step 2: Write the SQL

- IMPORTANT: Always enable Row Level Security on new tables (Supabase/Postgres):
  ```sql
  ALTER TABLE public.new_table ENABLE ROW LEVEL SECURITY;
  ```
- Make migrations idempotent when possible (`IF NOT EXISTS`, `IF EXISTS`)
- Add indexes on columns used in WHERE clauses, JOINs, and all foreign keys
- Add NOT NULL constraints where appropriate
- Use foreign key constraints to maintain data integrity
- Include a comment at the top describing what the migration does

Example structure:
```sql
-- Migration: Add tags table with many-to-many relationship to jobs
-- Date: YYYY-MM-DD

CREATE TABLE IF NOT EXISTS public.tags (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
  name TEXT NOT NULL,
  color TEXT DEFAULT '#6B7280',
  created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);

-- Enable RLS
ALTER TABLE public.tags ENABLE ROW LEVEL SECURITY;

-- RLS policies
CREATE POLICY "Users can manage own tags"
  ON public.tags FOR ALL
  USING (auth.uid() = user_id);

-- Indexes
CREATE INDEX idx_tags_user_id ON public.tags(user_id);
```

### Step 3: Update your ORM schema

Update your TypeScript schema to match the migration exactly:

```typescript
// Drizzle example
export const tags = pgTable('tags', {
  id: uuid('id').primaryKey().defaultRandom(),
  userId: uuid('user_id').notNull().references(() => users.id, { onDelete: 'cascade' }),
  name: text('name').notNull(),
  color: text('color').default('#6B7280'),
  createdAt: timestamp('created_at', { withTimezone: true }).notNull().defaultNow(),
});
```

If your migration tool uses a journal or metadata file (e.g., Drizzle's `_journal.json`), update that too — the tool won't detect the migration without it.

### Step 4: Update related types and schemas

If the migration affects shared types, enums, or validation schemas, update those too:
- TypeScript enum definitions
- Zod/Joi validation schemas
- API request/response types
- Any enum guardrails or build-time checks

### Step 5: Verify the build

```bash
# Run typecheck to ensure schema types are correct
npm run typecheck  # or pnpm typecheck

# Run linter to catch issues
npm run lint
```

### Step 6: Test locally (if local dev is set up)

```bash
# Supabase local
npx supabase db reset    # Drops and recreates DB, runs all migrations

# Prisma
npx prisma migrate dev   # Applies pending migrations to local DB

# Drizzle
npx drizzle-kit migrate  # Runs migrations against DATABASE_URL
```

If no local database exists, review the SQL carefully before deploying. Consider setting up local development to test migrations safely.

### Step 7: Deploy to production

```bash
# Supabase remote
npx supabase db push

# Prisma
npx prisma migrate deploy

# Drizzle (varies by setup — may run in build pipeline)
DATABASE_URL="..." npx drizzle-kit migrate
```

### Step 8: Verify

- Check your database dashboard to confirm the migration applied
- Test the affected API routes
- Update any schema documentation with the new table/column

## Rules

- NEVER modify an existing migration file that has been committed — always create a new one
- NEVER use `DROP TABLE` or `DROP COLUMN` without explicit user confirmation
- Always enable RLS on new tables (Supabase/Postgres)
- Always add indexes on foreign key columns
- Keep migrations small and focused — one concern per migration
- Always update metadata files (journal, checksum) if your tool requires them
- Always update schema documentation after adding a migration
- Test migrations locally before pushing to production when possible

## Common Patterns

### Adding a column
```sql
ALTER TABLE public.existing_table
ADD COLUMN IF NOT EXISTS new_column TEXT DEFAULT 'value';
```

### Adding an enum value
```sql
-- Note: IF NOT EXISTS for enum values requires Postgres 9.3+
ALTER TYPE status_enum ADD VALUE IF NOT EXISTS 'new_status';
```

### Creating a junction table (many-to-many)
```sql
CREATE TABLE IF NOT EXISTS public.job_tags (
  job_id UUID NOT NULL REFERENCES public.jobs(id) ON DELETE CASCADE,
  tag_id UUID NOT NULL REFERENCES public.tags(id) ON DELETE CASCADE,
  PRIMARY KEY (job_id, tag_id)
);

ALTER TABLE public.job_tags ENABLE ROW LEVEL SECURITY;
CREATE INDEX idx_job_tags_job_id ON public.job_tags(job_id);
CREATE INDEX idx_job_tags_tag_id ON public.job_tags(tag_id);
```

### Adding an index
```sql
CREATE INDEX IF NOT EXISTS idx_table_column ON public.table_name(column_name);
-- For partial indexes:
CREATE INDEX IF NOT EXISTS idx_active_users ON public.users(email) WHERE deleted_at IS NULL;
```

## Customization

Update this skill with your project-specific details:
- Replace migration directory paths with your actual paths
- Specify your migration tool and exact commands
- Add your ORM import patterns
- Add any build-time checks or guardrails your project uses
- Note whether you have local dev set up or run against remote only
