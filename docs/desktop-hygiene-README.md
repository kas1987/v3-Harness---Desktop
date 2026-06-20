# v3 Harness — Desktop

Desktop-hygiene harness for this Windows 11 workstation (i7-12700K, 64 GB, dev+prod mixed).
Its job: keep boot lean, background processes bounded, and every system change **reversible**.

> Repo: `github.com/kas1987/v3-Harness---Desktop` (to be renamed `v3` later).
> Distinct from `chromatic-harness-v2` (the **AI agent** harness). This one governs the *machine*, not agents.

## Docs

| Doc | Purpose |
|-----|---------|
| [docs/boot-inventory.md](docs/boot-inventory.md) | Canonical snapshot of what runs at boot: startup items, scheduled tasks, services, Docker containers, RAM baseline. |
| [docs/runbook.md](docs/runbook.md) | Operating principles + the reversible change-log from the 2026-06-02 cleanup, with exact restore commands. |
| [docs/postmortems/2026-06-02-clip-watcher-spawn-leak.md](docs/postmortems/2026-06-02-clip-watcher-spawn-leak.md) | The 54 GB runaway-spawn incident, root cause, fix, and the reusable watchdog pattern. |

## Operating principles

1. **Reversible by default** — disable, don't delete; quarantine, don't purge; back up registry keys before edits. Every change ships with a restore path.
2. **Bounded background work** — every long-running watcher needs a **singleton guard** and any watchdog must **reap-before-relaunch** (see post-mortem).
3. **Prod-aware trimming** — on a dev+prod mixed box, never disable autostart for anything the running service stack depends on (Docker, virtual-drive mounts) without verifying dependents first.
4. **Evidence over assertion** — baseline metrics (RAM, process counts) captured before/after every change.
