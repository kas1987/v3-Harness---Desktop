# Runbook

## Change-log — 2026-06-02 cleanup (all reversible)

### 1. Runaway-process leak fixed
1,132 `powershell.exe` + ~1,151 `conhost.exe` (54 GB) from a clip-watcher spawn loop — killed. RAM 78% → 43%. Root cause + fix: [postmortems/2026-06-02-clip-watcher-spawn-leak.md](postmortems/2026-06-02-clip-watcher-spawn-leak.md).

### 2. Startup trimmed
Removed Chrome auto-launch preload + OneDrive autostart; disabled Wispr Flow startup + Google Drive autostart. Docker Desktop + Google Drive virtual mount kept (prod dependents).
**Restore:** `reg import "C:\Users\kas41\.claude\state\startup-backup-2026-06-02.reg"` and move `Wispr Flow.lnk` out of `…\Startup\Disabled\`.

### 3. Stale state-file sweep
Removed 8 state files >7 days old from `~/.claude/state`.

### 4. Prism runtime retired (`D:\.04_Prism`)
13.6 GB runtime quarantined to `D:\_QUARANTINE_2026-06-02\04_Prism__from_D-root`; 33 scheduled tasks disabled; neo4j container stopped (volumes preserved).
**Restore:** `C:\Users\kas41\.claude\state\prism-retire-2026-06-02\RESTORE.ps1` (moves folder back, re-enables tasks, restarts neo4j).
Records: that folder's `MANIFEST.md` + `disabled-tasks.csv` + `scheduled-tasks-xml/`.

## Standard procedures

### Audit what runs at boot
```powershell
Get-CimInstance Win32_StartupCommand | Select Name,Command,Location
Get-ScheduledTask | ? {$_.State -ne 'Disabled' -and $_.Triggers.CimClass.CimClassName -match 'Logon|Boot'}
Get-Process | Group ProcessName | ? Count -gt 5 | Sort Count -Desc   # spot pile-ups early
```

### Reclaim RAM from a runaway spawn (general)
```powershell
# Identify the offender by parent + command line, then kill that pattern only:
Get-CimInstance Win32_Process -Filter "Name='powershell.exe'" |
  ? { $_.CommandLine -match '<script-name>' } |
  % { Stop-Process -Id $_.ProcessId -Force }
```

### Disable a startup item reversibly
- **Run key:** `reg export` the key first, then `Remove-ItemProperty`.
- **Startup folder:** move the `.lnk` into a `\Startup\Disabled\` subfolder.
- **Scheduled task:** `Export-ScheduledTask` to XML, then `Disable-ScheduledTask` (never delete).

## Gotchas observed on this box
- **Dangling junctions** read as empty/"path not found" on write while `Test-Path` returns true. `D:\.04_Prism\logs` → `D:\runtime\04-Prism\logs` had no target; create the target to repair. Check: `(Get-Item X).Attributes -band [IO.FileAttributes]::ReparsePoint`.
- **Windows PowerShell 5.1** can fail file writes that pwsh 7 / .NET handle fine — usually a reparse/volume quirk, not a real ACL issue.
- **Self-match:** a process scan filtering on a path string matches *its own* shell (whose command line contains that string). Always exclude `$PID`.
