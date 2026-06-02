# EPICS

Long-running threads of work that span multiple bd issues and/or multiple v2 PRs.

## Format

Each EPIC lives in its own `EPIC-<slug>.md` with:

```yaml
---
epic_id: EPIC-<slug>
opened: YYYY-MM-DD
status: planning | active | blocked | complete | abandoned
bd_issues: [chromatic-harness-v2-<hash>, v3-harness---desktop-<hash>, ...]
v2_prs: [<urls>]
---
```

Body sections (suggested):
- **Goal** — what done looks like
- **Why now** — driver / context
- **Plan** — waves, dependencies, checkpoints
- **Status log** — append-only timeline of state changes
- **Decisions referenced** — `decisions/*.md` linkbacks
