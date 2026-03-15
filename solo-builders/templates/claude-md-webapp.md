# Project: [Name]

## 1. Stack
- Next.js 15 (App Router) + TypeScript (strict mode)
- Supabase (auth, database, edge functions)
- Tailwind CSS + shadcn/ui
- Deployed on Vercel

## 2. Project Structure
- `app/` — Next.js App Router pages and layouts
- `components/` — Reusable UI components (shadcn/ui based)
- `lib/` — Shared utilities, Supabase client, types
- `supabase/` — Migrations, edge functions, seed data

## 3. Commands
- `npm run dev` — Start dev server
- `npm run build` — Production build (run before committing)
- `npm run lint` — ESLint check
- `npm run test` — Run vitest
- `npx supabase db push` — Push migrations to Supabase

## 4. Code Style
- Use ES modules (import/export), not CommonJS
- Functional components with hooks only
- Use `cn()` utility for conditional classNames
- Server Components by default; add 'use client' only when needed
- IMPORTANT: Always use TypeScript strict mode. No `any` types.

## 5. Git Workflow
- Create feature branches from main
- Commit after every working change with descriptive messages
- Run `npm run build` before committing — fix all errors
- Use conventional commits: feat(scope):, fix(scope):, chore(scope):

## 6. Testing
- Write tests for business logic and API routes
- Use vitest + React Testing Library
- Run single test files: `npx vitest run src/path/to/test.ts`

## 7. Common Mistakes to Avoid
- NEVER use `require()` — always `import`
- NEVER commit with TypeScript errors
- Don't create new dependencies without checking if existing ones solve the problem
- Don't use `div` with `onClick` — use `button` or appropriate semantic element
- Don't log PII (emails, names) to the console or server logs
