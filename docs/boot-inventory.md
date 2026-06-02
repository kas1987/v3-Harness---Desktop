# Boot Inventory — baseline 2026-06-02

Machine: Windows 11 Pro · i7-12700K (12C/20T) · 63.7 GB RAM. Profile: **dev + prod mixed** (single box runs dev work and hosted services).

## Startup items (HKCU `…\CurrentVersion\Run` + Startup folder)

| Item | State after 2026-06-02 cleanup | Notes |
|------|-------------------------------|-------|
| Claude | **kept** | primary tool |
| Docker Desktop | **kept** | prod stack (containers) depends on it |
| GoogleDriveFS | **disabled** | autostart removed; `G:` virtual mount (~1 TB) no longer auto-mounts at boot |
| GoogleChromeAutoLaunch | **removed** | `--no-startup-window` preload; pure boot waste |
| OneDrive | **disabled** | autostart removed |
| Wispr Flow (Startup folder) | **disabled** | moved to `\Startup\Disabled\` |
| Clip-to-MD, llm_capture (Startup folder) | kept | dev clipboard pipeline |

Backup of Run key: `C:\Users\kas41\.claude\state\startup-backup-2026-06-02.reg` (restore: `reg import`).

## Scheduled tasks

The `D:\.04_Prism` runtime drove **~35 tasks** (Prism-Jan janitor suite, Rudalo, OpenWebUI, FrontdoorSupervisor, clip pipeline, governance, routing-brain, ImagePrism weekly jobs). **33 were disabled** 2026-06-02 when the Prism runtime was retired (definitions exported to `~/.claude/state/prism-retire-2026-06-02/scheduled-tasks-xml/`).

Non-Prism logon/boot tasks that remain: OneDrive startup tasks, GoogleUpdater, PowerToys Autorun, MemoryMonitor-kas41, PM2-Resurrect-kas41, Opal Universe V3.

## Auto-start services (running)

Standard Windows + notable third-party: Docker (`com.docker.*`), `Grafana`, `Tailscale`, `NVDisplay.ContainerLocalSystem`, `WSAIFabricSvc`, `ssh-agent`, `SQLWriter`. Hyper-V (`vmms`) for WSL2.

## Docker containers (prod stack — left running)

| Container | Project | Notes |
|-----------|---------|-------|
| sim-simstudio / realtime / redis / db | `sim` (`E:\sim`) | SimStudio stack |
| prism-uptime-kuma | `uptime-kuma` (`E:\.06_Prism_Autonomy_Harness`) | monitoring |
| stash | `12_comftyui-harness` | |
| chromatic-brain-litellm | `litellm` | LLM proxy |
| harness-grafana / otel-collector | `04_observability` | |
| **neo4j** | `04_prism` (was `D:\.04_Prism`) | **stopped** 2026-06-02 with Prism retirement; named volumes `04_prism_neo4j-*` preserved |

## RAM baseline

| Point | Used | % |
|-------|------|---|
| Session start (with runaway leak) | 49.6 GB | 78% |
| After leak fix + Prism retirement | ~27 GB | ~43% |
