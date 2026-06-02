# Rollout: chromatic-harness-v2 multi-drive deployment

**Date:** 2026-06-02
**Workstation:** KSPC (desktop)
**Status:** ✅ Complete + verified
**Driver:** Claude Code session (Opus 4.7)

## What shipped

Deployed `kas1987/chromatic-harness-v2` to the desktop with NTFS-junction multi-drive topology and full activation including global config cutover.

## Topology delivered

| Drive | Path | Mode | Verified |
|-------|------|------|----------|
| **E** | `E:\chromatic-harness-v2` | Canonical clone | HEAD `9dc5acd` + 1 local `bd init --skip-agents` commit |
| **C** | `C:\chromatic-harness-v2` | NTFS junction → E | SHA256 byte-identity confirmed |
| **D** | `D:\chromatic-harness-v2` | NTFS junction → E | SHA256 byte-identity confirmed |
| **G** | *(excluded)* | — | Google Drive cloud-synced FAT32; junction refused by Windows; would corrupt `.git` |

## Side effects on the workstation

| Surface | Change | Backup |
|---------|--------|--------|
| `~/.claude/CLAUDE.md` line 53 | Path reference updated to new SoT + topology | `~/.claude/_backups/rollout-v2-20260602-120049/CLAUDE.md.bak` |
| `~/.claude/settings.json` | 5 SessionStart hooks archived (project settings now own boot) | `~/.claude/settings.json.pre-harness-slim.bak` + `~/.claude/settings.sessionstart-archived.json` |
| `~/.claude/workflows/` | 6 lite workflows installed (`_budget.js`, `close-issue.js`, `go.js`, `hotfix.js`, `qa.js`, `ship.js`) | Originals were absent (fresh install) |
| `~/.local/bin/bd.cmd` | Created — Windows-native subprocess shim for `bd` CLI | N/A (new file) |
| `E:\.00_Chromatic_Systems\` | Old REPO-SYSTEMS python harness archived | Moved to `E:\.89_Gits\ChromaticSystems_legacy_20260602` |

## Verification evidence

- All 10 governance gates PASS (`agent_operations`, `instruction_governance`, `instruction_drift`, `ide_parity`, `intake_loop`, `context_trim`, `pytest_workflows`, `pytest_guardrails`, `karpathy_discipline`, `claude_harness`)
- SHA256 `D796B1E25C99947FF90421CED3062EF7D8ECEB322337B7B6AD681CB80317B2B4` identical across C/D/E mirrors of `CLAUDE.md`
- Validator runs cleanly from C-drive cwd against C-drive junction
- Write-via-D → read-via-E + C → bytes match

## Findings

| Severity | Finding | Action |
|---|---|---|
| Yellow | `lock_wait_p95` 1516ms vs 1500ms threshold | Self-recovers; no action |
| P3 | 12 staged issues need `python scripts/seed_issues_to_beads.py --apply` | Apply when desktop intake begins |
| Lesson | Vanilla `bd init` pollutes v2 wrappers; **always use `--skip-agents --non-interactive`** | Documented + memorized; surfaced in [v2 PR #212](https://github.com/kas1987/chromatic-harness-v2/pull/212) |

## Upstream contributions

- [chromatic-harness-v2#212](https://github.com/kas1987/chromatic-harness-v2/pull/212) — `docs(deployment): add multi-drive rollout guide` (open for review)

## Loose ends (not blocking)

- v2 main has 1 local `bd init --skip-agents` commit (`e9f70fc`) not pushed. Workstation-specific personalization; intentionally local-only unless user directs push.
- Laptop workbook repo not yet created (no laptop session today).

## Rollback procedure

See [chromatic-harness-v2/09_DEPLOYMENT/MULTI_DRIVE_ROLLOUT.md](https://github.com/kas1987/chromatic-harness-v2/blob/docs/multi-drive-rollout-guide/09_DEPLOYMENT/MULTI_DRIVE_ROLLOUT.md) §Rollback. Local backup bundle at `~/.claude/_backups/rollout-v2-20260602-120049/`.
