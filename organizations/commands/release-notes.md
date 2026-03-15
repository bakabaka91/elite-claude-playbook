Generate release notes from git history for version: `$ARGUMENTS`

Usage: `/release-notes 2.5.0`

1. **Read commit history** from the last tag to HEAD:
   ```bash
   git log $(git describe --tags --abbrev=0)..HEAD --oneline
   ```
   Capture all commit messages.

2. **Categorise commits** by type:
   - **Features** — commits starting with `feat:` or `feature:`
   - **Fixes** — commits starting with `fix:` or `bugfix:`
   - **Breaking Changes** — commits with `BREAKING CHANGE:` in body, or major version bump
   - **Deprecations** — commits that deprecate functionality
   - **Performance** — commits with `perf:`
   - **Chores** — documentation, deps, tests (usually omit from release notes)

3. **Check for migration steps**:
   - Does this release have breaking changes?
   - If yes, write a Migration Guide section explaining:
     - What changed
     - How to update code
     - Timeline (when old version is sunsetted)
   - Provide code examples if applicable

4. **Generate the release notes markdown:**

```markdown
# Release [VERSION]

## New Features
- [Feature 1 from git log]
- [Feature 2 from git log]

## Bug Fixes
- [Fix 1 from git log]
- [Fix 2 from git log]

## Breaking Changes
- [Describe breaking change 1]
- [Describe breaking change 2]

## Deprecations
- [Deprecated feature 1] — will be removed in [future version]
- [Deprecated feature 2] — migrate by [date]

## Performance Improvements
- [Perf improvement 1]
- [Perf improvement 2]

## Migration Guide
[Only if breaking changes]

### Before
```typescript
// Old code pattern
```

### After
```typescript
// New code pattern
```

## Contributors
- [Name 1] ([commit count])
- [Name 2] ([commit count])

## Release Date
[YYYY-MM-DD]
```

5. **Write to file:** `docs/releases/v[VERSION].md`

6. **Commit the release notes:**
   ```bash
   git add docs/releases/v[VERSION].md
   git commit -m "docs: release notes for v[VERSION]"
   git tag -a v[VERSION] -m "Release v[VERSION]"
   git push origin main --tags
   ```

**Do not merge** to main without release notes for the next version being planned.
