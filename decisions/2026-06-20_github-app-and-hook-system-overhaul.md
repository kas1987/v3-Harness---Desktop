# Decision: GitHub App Integration + Hook System Overhaul
**Date:** 2026-06-20
**Session:** eb631fe3-b02a-4cd9-b5c6-712ca56b045b
**Status:** Complete — all changes pushed pending

---

## What was decided

### 1. GitHub App (`chromatic-self`) fully wired

- **App ID:** 3951542 | **Client ID:** Iv23liJUeS0F3aqKyiKr | **Installation ID:** 137727894
- **PEM:** `C:\Users\kas41\.agents\github-app\private-key.pem`
- **Token generator:** `C:\Users\kas41\.agents\github-app\generate-token.py`
- **Webhook receiver:** `C:\Users\kas41\.agents\github-app\webhook-receiver.py` (port 9421)
- **Config:** `C:\Users\kas41\.agents\github-app\config.json`
- `gh` CLI now authenticated as `chromatic-self[bot]` (ghs_ token) — full access to all repos including private
- Personal OAuth (`kas1987`) retained as inactive fallback — switch with `gh auth switch -u kas1987`

**Scheduled tasks added:**
- `GitHub-Webhook-Receiver` — starts at logon, restarts on failure
- `GitHub-App-Token-Refresh` — daily 8am, keeps token cache warm
- `OL-Queue-Drain` — daily 9am + logon, drains `actions.jsonl` backlog (80 items cleared)

### 2. Hook system fixes

| Hook / Setting | Change | Reason |
|---|---|---|
| `SUBAGENT_ROUTING_ENFORCE` | `strict` → `warn` | Was blocking all fork spawns without 3-part context packet |
| `task_complete_gate.py` — OL kill switch | Added `~/.claude/state/ol_gate_disabled` sentinel | Needed to unblock Haiku sessions immediately |
| `task_complete_gate.py` — `HIGH_COMPLEXITY_RE` | Narrowed regex — removed `architecture`, `workflow`, `queue` etc | False-positive rate was too high; common words triggered OL escalation |
| `settings.json` — SessionStart hooks | Added: `settings-lock.py`, `context-loader.py`, `lifecycle-writer-health.py` | Were listed in REQUIRED_HOOKS but missing from settings — caused T3 spam on every write |
| `settings.json` — UserPromptSubmit hooks | Added: `budget-advisory.py` (async) | Same — was in REQUIRED_HOOKS, not wired |
| `settings-lock.py` — REQUIRED_HOOKS | Removed `ollama_compact_prep.py` | Script doesn't exist on disk |
| `compact-git-sync.sh` | Branch guard (skip push on main/master) + push opt-in via `COMPACT_GIT_PUSH=true` | Was pushing WIP code to shared branches on every compaction |
| `memory-health-guard.py` | No change — kept at both Stop + PreCompact | Intentional: PreCompact trims silently, Stop writes audit report |

### 3. Findings (no change made)

- **OL queue consumer gap:** `actions.jsonl` had 69KB of unexecuted items — now drained and consumer task running
- **GitHub auth:** Standard OAuth (`gho_`) was sufficient for dev work; App token (`ghs_`) adds automation + CI capability
- **No GitHub App previously configured** on this machine — this session was first-time setup

---

## Pending (next session)

- [ ] Set webhook secret in GitHub App settings + add `GITHUB_WEBHOOK_SECRET` to `settings.json`
- [ ] Expose webhook receiver publicly (ngrok or Cloudflare Tunnel) for real GitHub event delivery
- [ ] `gh auth switch -u kas1987` for normal dev work; use bot account for automation only
- [ ] Commit + push all session changes (see file list below)
- [ ] Decide whether to re-enable OL gate (`rm ~/.claude/state/ol_gate_disabled`) after narrowed regex is validated

## Files changed this session

```
C:\Users\kas41\.claude\settings.json           — routing enforce + SessionStart/UserPromptSubmit hooks
C:\Users\kas41\.claude\hooks\task_complete_gate.py  — kill switch + narrowed regex
C:\Users\kas41\.claude\hooks\settings-lock.py   — removed ghost ollama_compact_prep.py
C:\Users\kas41\.claude\hooks\compact-git-sync.sh — branch guard + push opt-in
C:\Users\kas41\.agents\github-app\config.json        — NEW: App config
C:\Users\kas41\.agents\github-app\generate-token.py  — NEW: JWT → installation token
C:\Users\kas41\.agents\github-app\webhook-receiver.py — NEW: webhook server
C:\Users\kas41\.agents\github-app\discover-install.py — NEW: one-time discovery script
C:\Users\kas41\.agents\dispatcher\ol-queue-drain.py   — NEW: queue consumer
C:\Users\kas41\.claude\state\ol_gate_disabled         — NEW: kill switch sentinel (active)
```
