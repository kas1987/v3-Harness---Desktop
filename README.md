# v3-Harness — Desktop (KSPC)

Desktop-specific **workbook** for chromatic-harness operations.

## What this repo is

This is **not** a fork of `chromatic-harness-v2`. It is the desktop workstation's session-level governance surface:

- **Issues, EPICs, beads database** for desktop work
- **Decision log** for rollouts, cutovers, machine-state changes
- **Machine-specific overrides** and operating notes
- **Rollout artifacts** (deployment guides scoped to this workstation)

The actual harness **code** still lives at `kas1987/chromatic-harness-v2`. This workbook references it.

## What this repo is NOT

- Not a place to land harness runtime changes (those go to v2)
- Not a place to fork v2's code (would create sync hell)
- Not shared with the laptop (laptop has its own workbook or none)

## Topology

| Layer | Repo / Path | Owner | Notes |
|-------|-------------|-------|-------|
| Harness runtime | `kas1987/chromatic-harness-v2` → `E:\chromatic-harness-v2` | Shared between machines | Both desktop + laptop clone this; PRs go upstream |
| Desktop workbook | `kas1987/v3-Harness---Desktop` → `E:\v3-Harness---Desktop` | Desktop only (KSPC) | bd DB, EPICs, rollout notes, machine state |
| Laptop workbook | *(separate, TBD)* | Laptop only | Mirror pattern if desired |

## Directory layout

```
.beads/              — beads database (initialized with --skip-agents)
EPICS/               — long-running EPIC trackers + RFC-style design docs
decisions/           — append-only decision log (one .md per decision)
rollouts/            — rollout artifacts and runbooks scoped to this workstation
machine/             — KSPC-specific config snapshots, hardware notes, env captures
inbox/               — raw notes / capture before triage
```

## Daily loop

1. New work intent → file as bd issue in this repo, optionally promote to EPIC
2. Code changes → branch on `chromatic-harness-v2`, open PR there
3. Cross-link the bd issue ↔ v2 PR via URL in both directions
4. On completion: close bd here, merge PR on v2
5. Material decisions captured in `decisions/`

## Why this is "safer" than running everything from v2

- v2 main can't accidentally absorb workstation-personalization commits
- Force-push to this repo only affects desktop tracking, never the shared codebase
- Beads DB pollution stays contained
- Each machine's `MULTI_DRIVE_ROLLOUT.md`-style notes diverge naturally without merge churn

## Related

- Multi-drive rollout pattern: see `rollouts/2026-06-02_v2-multi-drive-rollout.md` (also proposed upstream as [chromatic-harness-v2#212](https://github.com/kas1987/chromatic-harness-v2/pull/212))
