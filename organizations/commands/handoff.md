Create a comprehensive handoff document for the next engineer. This is critical at org scale — work that takes hours of context to ramp into should be documented, not lost.

1. **Session summary** — what did you do this session? (2-3 sentences)
2. **Work completed** — list finished items with commit hashes or PR numbers
3. **Work in progress** — what's started but not finished?
   - Current state (what works, what doesn't)
   - Next steps (what should the next person do first)
   - Blockers or unknowns (anything you got stuck on?)
4. **Open questions** — anything you couldn't figure out? (list them)
5. **Architecture decisions** — did you change how something works? (explain why)
6. **Testing coverage** — what tests passed? What still needs testing?
7. **Performance considerations** — is there anything slow that the next person should know about?
8. **Configuration or environment changes** — did you add any env vars, secrets, or config?
9. **Team or stakeholder communication** — did you need to notify anyone? What did you say?
10. **Time spent** — estimate: how many hours on this task? (so the next person knows scope)

**Write to:** `docs/handoffs/[YYYY-MM-DD]-[feature-name].md`

The next engineer will read this before picking up your work. Make their job easy. Save them 1 hour of re-context by taking 10 minutes to write this.
