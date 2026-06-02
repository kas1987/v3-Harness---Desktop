# Rollout: AI model SoT consolidation to `D:\.000_AI`

**Date:** 2026-06-02 (afternoon)
**Workstation:** KSPC (desktop)
**Status:** ✅ Complete + verified (Ollama live, LM Studio rewired, HF cache moved)
**Driver:** Claude Code session (Opus 4.7)
**Companion rollout:** [`2026-06-02_v2-multi-drive-rollout.md`](2026-06-02_v2-multi-drive-rollout.md) (harness deploy earlier same day)

## Goal

All LLM runtimes (Ollama, HuggingFace cache, LM Studio) pulling and operating from a single source of truth at `D:\.000_AI`, sitting alongside the existing ComfyUI ecosystem (`D:\.000_AI\Models\`, 344 GB — untouched per user).

## Before → After

| Runtime | Before path | After path | Size |
|---------|-------------|------------|------|
| Ollama | `D:\AI\.01_models\.01_ollama\` | `D:\.000_AI\Ollama\` | 69.5 GB, 60 files |
| HuggingFace cache (HF Hub + vLLM + transformers) | `E:\AI_Models\LLM\HuggingFace\` | `D:\.000_AI\HuggingFace\` | 0.09 GB |
| LM Studio | `C:\Users\kas41\.lmstudio\models\lmstudio-community` → `E:\AI\lmstudio-community` → `D:\AI\lmstudio-community` (BROKEN chain) | `C:\Users\kas41\.lmstudio\models\lmstudio-community` → `D:\.000_AI\LMStudio\lmstudio-community` (NTFS junction) | ~4 GB hub state on C (left), models→D |
| ComfyUI | `D:\.000_AI\Models\` | `D:\.000_AI\Models\` | 344 GB (UNTOUCHED) |

## Env vars set (User + Machine scope, both)

```
OLLAMA_MODELS          = D:\.000_AI\Ollama
HF_HOME                = D:\.000_AI\HuggingFace
TRANSFORMERS_CACHE     = D:\.000_AI\HuggingFace
HUGGINGFACE_HUB_CACHE  = D:\.000_AI\HuggingFace
LMSTUDIO_MODELS_DIR    = D:\.000_AI\LMStudio   (User scope only — LM Studio reads settings.json primarily)
```

## LM Studio app settings touched

`C:\Users\kas41\.lmstudio\settings.json`:
- `downloadsFolder`: `"E:\\AI"` → `"D:\\.000_AI\\LMStudio"`

LM Studio app needs to be relaunched to pick up the new setting.

## Verification evidence

**Ollama (live):**
- Server restart log shows `OLLAMA_MODELS:D:\.000_AI\Ollama`
- `total blobs: 47` detected at new location
- NVIDIA RTX 4070 (12 GB VRAM, 10.8 GB available) detected
- `GET /api/tags` returned **13 models**, including: `gemma4:latest`, `qwen2.5-coder:14b`, `deepseek-r1:14b`, `phi4:latest`, `qwen3:14b`

**HuggingFace:**
- Cache content (`xet/` subdir) moved cleanly; source dir removed
- Env vars set in both User and Machine scope
- Next process to use HF Hub or vLLM will see the new path

**LM Studio:**
- Old broken junction removed cleanly (it was pointing through a dead E:\AI → D:\AI → no `lmstudio-community` chain)
- New NTFS junction created: `C:\Users\kas41\.lmstudio\models\lmstudio-community` → `D:\.000_AI\LMStudio\lmstudio-community`
- App must be restarted for new settings.json to take effect

## E:\ root cleanup (companion work)

- Removed accidental `bd init` pollution at E:\ root (`.git`, `.beads`, `.gitignore`, `CLAUDE.md`, `AGENTS.md`) — created during Wave 0 of the harness rollout before the `--skip-agents` lesson was learned
- Quarantine purged across all three drives:
  - `E:\_QUARANTINE_2026-06-02` (33.4 GB)
  - `C:\Users\kas41\_QUARANTINE_2026-06-02` (36.5 GB) — required `\\?\` extended-path bypass for Windows-reserved `nul` filename
  - `D:\_QUARANTINE_2026-06-02` (17.4 GB) — required extended-path bypass for permission-denied files
  - **Total freed: ~87 GB**

## Open follow-ups

- ⚠️ **`E:\.06_Prism_Autonomy_Harness` STILL HELD by an OS file lock** (Defender/Search/mmap — unattributable). Manifest predicted this needs a reboot to release. Tracked as bd issue. After reboot:
  ```powershell
  Move-Item 'E:\.06_Prism_Autonomy_Harness' 'E:\_QUARANTINE_2026-06-02\.06_Prism_Autonomy_Harness' -Force
  ```
  Since the quarantine bundle is already purged, this would effectively just be a delete now. Simpler: `Remove-Item 'E:\.06_Prism_Autonomy_Harness' -Recurse -Force` post-reboot.
- LM Studio relaunch needed for `settings.json` to take effect
- Future model downloads (any runtime) will now land at `D:\.000_AI\<Runtime>\` by default

## What changed about how to talk to each runtime

Nothing externally. Each runtime's API surface (ollama on `localhost:11434`, LM Studio on its app port, HF Hub via python imports) is unchanged. The change is purely about *where the bytes live on disk*.

## Layout under D:\.000_AI now

```
D:\.000_AI\
├── Models\           (344 GB — ComfyUI ecosystem, untouched)
├── ComfyUI\          (ComfyUI install, untouched)
├── Ollama\           ← NEW (69 GB)
│   ├── blobs\
│   └── manifests\registry.ollama.ai\
│       ├── library\{gemma4,qwen3,deepseek-r1,phi4,qwen2.5-coder,llava,...}
│       └── huihui_ai\qwen3-vl-abliterated\4b-instruct\
├── HuggingFace\      ← NEW (0.09 GB, grows as HF Hub + vLLM are used)
│   └── xet\
├── LMStudio\         ← NEW (empty, accepts new downloads)
│   └── lmstudio-community\   (junction target from C:\Users\kas41\.lmstudio\models\)
├── Sessions\         (existing)
├── TTS\              (existing)
└── ... (AGENTS.md, CLAUDE.md, MODEL_CATALOG.json, MODEL_INDEX.json, etc.)
```
