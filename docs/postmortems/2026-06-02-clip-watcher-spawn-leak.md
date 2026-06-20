# Post-mortem — clip-watcher spawn leak (2026-06-02)

## Impact
1,132 `powershell.exe` + ~1,151 `conhost.exe` processes consuming **54 GB** of 64 GB (system at 78% RAM). Accumulated ~1/min over ~19 hours.

## Root cause
`Prism-Clip-Watchdog` (Task Scheduler, repeat = `PT1M`) launched `start-clip-watcher.ps1` whenever its check was `(-not watcher_alive OR heartbeat_stale)`. Two defects compounded:

1. **No reap-before-relaunch** — the watchdog never killed the existing/stale instance before launching a new one.
2. **No singleton guard** — nothing stopped a second watcher from running.

So whenever a watcher's heartbeat went stale while the process was still technically alive (hung clipboard call / STA contention), every 60 s tick spawned *another* watcher and orphaned the old one. Unbounded growth.

## Fix
1. **Watchdog** (`clip-watchdog.ps1`): rewrote restart logic to enumerate **all** watcher instances, no-op when exactly one healthy watcher exists, and **reap every instance before relaunching exactly one**. Verified stable at 1 across repeated ticks.
2. **Watcher** (`clip-watcher.ps1`): added a `Global\Prism-Clip-Watcher` mutex — a second instance self-exits. Defense-in-depth + resolves the logon-task vs. watchdog dual-launch race.
3. Killed the 1,132 runaways → reclaimed ~22 GB immediately; conhost siblings collapsed 1,151 → 38.

> Scripts lived at `D:\.04_Prism\scripts\clipboard\` (now in quarantine with the retired Prism runtime). The *pattern* below is the durable takeaway.

## Reusable pattern — any background watcher/watchdog
- **Singleton guard in the worker:** acquire a named mutex (`Global\<name>`) at startup; if not `createdNew`, exit.
- **Watchdog must reap-before-relaunch:** enumerate all instances → if exactly one and healthy, do nothing → otherwise kill all, then launch one.
- **Heartbeat ≠ liveness:** "process exists" is not "process healthy." A stale heartbeat on a live PID must trigger a *replace* (kill+start), never an *additional* start.
- **Cap + alarm:** if instance count ever exceeds 1, log it loudly — that's the early signal of this class of bug.

## Detection that would have caught it sooner
`Get-Process | Group ProcessName | ? Count -gt 5` at session start surfaces pile-ups instantly. Worth a recurring scan (deferred — see seed-scope decision).
