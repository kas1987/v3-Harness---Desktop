# Machine: KSPC (desktop)

**Hostname:** KSPC
**Manufacturer:** ASUS (custom build)
**OS:** Windows 11 Pro 10.0.26200
**Shell:** PowerShell 7+ / bash via git-bash
**Python:** 3.11.9 (system) at `C:\Users\kas41\AppData\Local\Programs\Python\Python311\`
**Node:** v24.8.0
**pnpm:** 10.14.0 (via `npm exec`)
**Docker:** 29.4.3
**Git:** `core.safecrlf=false`

## Drives

| Drive | Filesystem | Size used | Size free | Role |
|-------|-----------|-----------|-----------|------|
| C | NTFS | ~960 GB | ~970 GB | System + tools (.cargo, .local, .lmstudio, etc.) |
| D | NTFS | ~1.03 TB | ~893 GB | Project storage; some `_Persona_Metadata`, `_3d-Meta`, etc. |
| E | NTFS | ~42 GB | ~1.96 TB | **Canonical** for chromatic-harness-v2 + v3-Harness-Desktop |
| G | FAT32 (Google Drive virtual) | ~1.0 TB | ~921 GB | Cloud-synced; **excluded from harness junctions** |

## Junctions live on this machine

- `C:\chromatic-harness-v2` → `E:\chromatic-harness-v2`
- `D:\chromatic-harness-v2` → `E:\chromatic-harness-v2`
- *(v3 junctions added per same pattern; see decisions/2026-06-02_initial-topology.md)*

## Critical PATH entries

- `C:\Users\kas41\.local\bin\` — contains `bd.cmd` shim (Windows-native subprocess interop with beads)
- `C:\Users\kas41\.cargo\bin\` — Rust toolchain
- `C:\Users\kas41\bin\` — bash-only `bd` shim (legacy; superseded by `.local\bin\bd.cmd` for Win-native callers)

## Global Claude Code config touch points

- `C:\Users\kas41\.claude\CLAUDE.md` — global constitution; line 53 updated 2026-06-02 to reference new harness path
- `C:\Users\kas41\.claude\settings.json` — SessionStart hooks slimmed by harness production script
- `C:\Users\kas41\.claude\settings.sessionstart-archived.json` — preserved global hooks
- `C:\Users\kas41\.claude\workflows\` — 6 lite workflows from v2
- `C:\Users\kas41\.claude\projects\E--\memory\` — auto-memory store for E-drive sessions

## Backups

- `C:\Users\kas41\.claude\_backups\rollout-v2-20260602-120049\` — pre-rollout snapshot
- `C:\Users\kas41\.claude\settings.json.pre-harness-slim.bak` — pre-slim settings

## Known machine quirks

- Google Drive on G makes G appear as a local drive but it's cloud-synced; never put a working git repo there (corrupts `.git`)
- `bd` was installed as a bash shim at `~/bin/bd`; Windows-native subprocesses don't see it without `~/.local/bin/bd.cmd` shim
- Rollup logs occasionally exceed lock_wait_p95 threshold (1500ms) — not a real problem, self-recovers
