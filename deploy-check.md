Run a full pre-deployment check. Execute each step and report pass/fail:

1. **TypeScript** — run typecheck (must pass with zero errors)
2. **Lint** — run linter (must pass with zero errors)
3. **Build** — run production build (must complete successfully)
4. **Tests** — run test suite (all tests must pass)
5. **Git status** — check for uncommitted changes (warn if any)
6. **Security scan** — grep for hardcoded API keys, leftover console.log statements, and `any` types in TypeScript

Report results as a checklist:
- Use a checkmark or X for each step
- If any step fails, show the error and suggest a fix
- Final verdict: READY TO DEPLOY or BLOCKED (with reasons)
