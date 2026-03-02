Create a new database migration for: $ARGUMENTS

Follow this workflow:

1. **Check existing schema** — read the latest migrations and ORM schema to understand current state
2. **Create the migration file** — use your migration tool (Supabase CLI, Drizzle Kit, Prisma, etc.)
3. **Write the SQL** — make it idempotent when possible (IF NOT EXISTS, IF EXISTS)
4. **Update ORM schema** — update your TypeScript schema to match the migration
5. **Test locally** — run migration locally to verify (if local dev is set up)
6. **Show for review** — display the migration SQL for review before applying to production

IMPORTANT: Always enable Row Level Security (RLS) on new tables if using Supabase/Postgres.
IMPORTANT: Always add indexes on foreign key columns.
IMPORTANT: Never modify an existing committed migration file — create a new one.
IMPORTANT: Keep migrations small and focused — one concern per migration.
