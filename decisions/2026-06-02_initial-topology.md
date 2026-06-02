# Decision: v3 workbook topology

**Date:** 2026-06-02
**Status:** Active
**Workstation:** KSPC (desktop, ASUS, Win11 Pro)
**Decided with:** Claude Code (Opus 4.7) during chromatic-harness-v2 multi-drive rollout

## Context

Earlier this session the chromatic-harness-v2 repo was deployed to the desktop with E as canonical SoT and NTFS junctions on C and D. After completion, the user observed that operating *every* harness change from v2 main carries risk:

- Bd init commits, settings.json edits, and machine-specific personalization were ending up as local commits on v2 main, requiring care not to push upstream by accident.
- The laptop will eventually run from the same v2 origin, so v2 main needs to stay clean of workstation-specific noise.
- Cross-machine governance (issues, EPICs, decision history) was conflating with code.

## Decision

Two-repo split:

- **`kas1987/chromatic-harness-v2`** — shared harness CODE. Either machine clones it; PRs go upstream; runtime/script changes live here. Each machine's local clone may have personalization commits that **never** get pushed.
- **`kas1987/v3-Harness---Desktop`** — desktop-only workbook. Owns bd database, EPIC trackers, decision log, rollout artifacts, machine-state snapshots. Force-pushes here are safe because the laptop doesn't read this repo.

Laptop can adopt the mirror pattern (a `v3-Harness---Laptop` or similar) if/when it needs the same separation. Until then, the laptop runs harness from v2 directly.

## Consequences

**Positive:**
- v2 main stays clean of machine personalization
- Desktop EPIC/issue tracking is fully isolated from laptop tracking
- Force-push is cheap on workbook; impossible-by-rule on shared code
- Two distinct "blast radii" for risky operations

**Negative:**
- Two repos to keep open; some context-switching overhead
- Cross-linking discipline required (bd issues ↔ v2 PRs)
- Slight risk of decision log drifting from code reality if cross-links aren't maintained

## Rejected alternatives

- **Single repo, branch-per-machine.** Rejected — branches are for in-flight work, not durable machine identity. Would invite accidental merges.
- **Fork v2 to v3 (full code mirror).** Rejected — divergence pressure between forks would create perpetual rebase debt with no compensating benefit. Desktop and laptop running same harness CODE is a feature, not a problem to solve.
- **No v3 repo, just careful local discipline on v2.** Rejected by user — "more safely" was the explicit ask. Discipline alone isn't a safety mechanism.
- **v3 as workstation-specific FORK of v2 (selective sync via cherry-pick).** Considered but deferred. Could be a future evolution if desktop genuinely needs to diverge in code, but YAGNI today.

## Open questions

- Does the laptop want its own workbook repo? Defer to laptop session.
- Should v3 import v2's `.agents/` or related runtime state via submodule for richer context? Probably not — workbook stays markdown-and-bd, no runtime.
- What's the sync cadence for desktop workbook → shared knowledge (forge-style harvesting back into v2 docs)? Open.
