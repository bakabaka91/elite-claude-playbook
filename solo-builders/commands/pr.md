Prepare a pull request for the current branch:

1. **Review changes** — read all changed files compared to main
2. **Clean up** — fix any lint errors, formatting issues, or TypeScript warnings
3. **Remove debug code** — remove console.log statements and commented-out code
4. **Run checks** — execute typecheck, lint, and tests — fix any failures
5. **Stage everything** — git add all changes
6. **Commit** — create a descriptive commit following conventional commits: feat(scope):, fix(scope):, perf(scope):, chore(scope):
7. **Push** — push the branch to origin
8. **Create PR** — use GitHub CLI or MCP to create a pull request with:
   - Title following conventional commit format
   - Summary of all changes
   - Testing notes (what was tested, how to verify)
   - Any breaking changes or migration steps

If any check fails, fix the issue first before creating the PR.
