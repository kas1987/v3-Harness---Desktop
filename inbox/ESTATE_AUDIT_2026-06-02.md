# Estate Audit & v3 Harness Prep — 2026-06-02

**Mode:** Audit-only. Zero destructive operations performed. All "delete" / "move" entries below are *proposals* awaiting explicit approval.

**Goal:** Inventory the C/D/E/G estate, classify each top-level item, surface a phased cleanup + migration kill list, and propose the v3 harness landing architecture.

**Doc actual location:** `E:\.89_Gits\ChromaticSystems_legacy_20260602\v3-prep\` — the legacy harness was renamed during a parallel cleanup pass. Move this doc to wherever `chromatic-harness-v2` lands once that repo is cloned to E:.

---

## §-1 — Already In Flight (same date — prior session)

Per `~/.claude/projects/C--Users-kas41/memory/project_drive_cleanup_2026-06-02.md` and `_QUARANTINE_2026-06-02\MANIFEST.md` on C/D/E:

- **Disposal pattern = quarantine, not delete.** Items move to `_QUARANTINE_2026-06-02\` on the same drive. Fully reversible. **All "delete" recommendations below should be read as "quarantine" instead.**
- **v3 target = `chromatic-harness-v2`** (GitHub, public, pushed 2026-06-02). Being set up fresh on **E:**, user-handled. Original `E:\.00_Chromatic_Systems\` was renamed → `E:\.89_Gits\ChromaticSystems_legacy_20260602\`. **Do not touch the chromatic-harness-v2 clone.**
- **39 items already quarantined.** Includes my entire "Cohort D" (28 abandoned AI-tool dotfiles in C home from 2026-02-24 batch), 6 clean clones in `E:\.89_Gits`, 5 pre-existing trash folders. **§4 Phase 3 "Cohort D bulk-delete" is already done.**
- **FLAGGED repos NOT moved (uncommitted/unpushed work — would lose data):**
  - In `E:\.89_Gits`: 04-Prism, agentops, ChromaticSystems, ChromaticSystems_legacy_20260602, ComftyUI-Harness, Command-Center, Image-Org, Image-Prism, The-Veil
  - `D:\prism-autonomy-harness` (no upstream; origin **archived** — canonical is `Poly-Chromatic/prism-autonomy-harness`)
  - **Reconcile each (commit+push or confirm disposable) before quarantining.**
- **Deferred:** `D:\_archive\Vids` (106 G), `D:\.archive-minimal-repos`, canonical pairs: `claude-config` (D vs E), `Image-Prism` (×3), `D:\3D_Meta` vs `D:\.11_3d-Meta`.
- **Junctions clarified (NOT duplicates):** `.101_Persona_Metadata_Atlas`, `.20_Center_Mass`, `AI`, `.11_3d-Meta` on E: are E→D junctions. Data lives once on D.

**Audit adds on top:** drive-root file pollution, heavy stale Cohort C dotfiles (~50G), `D:\.000_AI\.git` 114G anti-pattern, `D:\.000_AI\Models` vs `D:\AI` duplication, `$Recycle.Bin` 72G, `$Windows.~BT` 14.87G, governance-root consolidation, v3 architecture proposal aligned to `chromatic-harness-v2`.

---

## §0 — Executive Summary

| Drive | Used | Free | Role today | v3 target role |
|-------|------|------|-----------|----------------|
| C: | 958 G | 903 G | OS + home + project leftovers | OS + home only (no projects) |
| D: | 1031 G | 831 G | Data / projects / archives (mixed) | Sole data + project home |
| E: | 41 G  | 1821 G | Harness root + junctions to D: | **Sole harness/governance home** |
| G: | 2.6 G | n/a | Google Drive sync | Out of scope (cloud) |

**Headline numbers:**
- **~210 GB recoverable on quick wins alone** (recycle bin 72 G + Windows upgrade leftovers 15 G + abandoned project 41 G + obvious dupes 19 G + heavy abandoned dotfiles ~50 G + archived video 106 G — review).
- **~67 GB in home dotfiles** from one-time IDE/tool experiments, much of it stale ≥3 months.
- **Two parallel governance roots in $HOME** (`~/.claude/` and `~/.harness/`) duplicating CLAUDE.md / AGENTS.md / GLOBAL_MEMORY.md — must consolidate in v3.
- **D: contains likely-duplicate AI trees** (`.000_AI/Models` 390 G vs `AI/.01_models` 69 G) and a **114 GB `.git` repo inside `.000_AI`** (anti-pattern — model files tracked in git).
- **E: is 98% empty.** Harness footprint is only ~40 GB on a 2 TB drive. Natural v3 landing zone.

**Recommended sequencing:** Phase 1 (zero-risk reclamation, ~75 GB) → Phase 2 (verified-safe deletions, ~135 GB) → Phase 3 (governance consolidation) → Phase 4 (v3 scaffold on E:) → Phase 5 (project migration from C:/D: to v3 layout).

---

## §1 — Drive-by-Drive Findings

### C:\ (958 G used) — should be OS only

**Loose files at C:\ root (DELETE candidates):**
```
tmp_comfy_fix.json                    (2026-05-24, 33 KB)
tmp_bd_out.txt                        (2026-05-25, 2 KB)
Output.txt                            (2026-02-26, 1 KB)
DumpStack.log / DumpStack.log.tmp     (2025-11/2026-06)
migration_log_20260127_180741.csv     (2026-01-27)
three_stage_test_output.log           (2025-11-19)
plan-subRegionalTaxonomy.prompt.md    (2026-02-01)
PROJECT_MIGRATION.ps1                 (2025-10-06)
SAFE_DELETE_OLD_COMFYUI.ps1           (2026-01-27 — work already executed)
DELETE_OLD_COMFYUI.bat                (2026-01-27 — work already executed)
C_DRIVE_ANALYSIS_AND_MIGRATION_PLAN.md (2026-01-27 — historical, archive)
C_DRIVE_DELETION_SUMMARY.md           (2026-01-27 — historical, archive)
appverifUI.dll / vfcompat.dll         (legitimate? confirm before delete)
```

**Top-level dirs on C:\ — classification:**

| Dir | Size | mtime | Class | Action |
|-----|------|-------|-------|--------|
| `$Recycle.Bin` | **71.82 G** | — | OS | **EMPTY** (zero risk) |
| `.02_Zceleb` | 40.50 G | 2026-01-04 | project (stale 5mo) | **MIGRATE → D:** |
| `.00_AI Inventory` | 19.73 G | 2026-01-27 | partial-cleanup remainder | **INVESTIGATE then delete/migrate** |
| `xttsenv` | 4.51 G | 2025-08-12 | abandoned python venv (10mo old) | **DELETE** (recreate if needed) |
| `Temp` | 0.30 G | 2026-05-23 | OS temp | **PURGE contents** |
| `tmp` | 0.02 G | 2026-05-26 | stray | **DELETE** |
| `VEIL` | 0 G | 2026-04-07 | empty stub | **DELETE** |
| `Zelex`, `root`, `Repositories`, `Dev`, `opal_assistant`, `memories`, `MaterialIcons`, `logs`, `datasets`, `chromatic-inbox-harness-data`, `c`, `Assets`, `AI-DJ`, `Scripts` | 0 G each | various | empty/stub | **DELETE** (verify empty first) |
| `Documents and Settings` | 0 G | 2025-07-26 | OS junction → `C:\Users` | KEEP (system) |
| `inetpub`, `PerfLogs`, `Recovery`, `OneDriveTemp`, `EFSTMPWP`, `System Volume Information`, `Config.Msi` | 0 G | various | OS | KEEP |
| `.git`, `.claude` (root level) | 0 G | — | stray | **INVESTIGATE** (top-level `.git` at C:\ root is unusual) |
| `Python313` | 0.04 G | 2025-10-22 | system python | KEEP |
| `Users`, `Program Files`, `Program Files (x86)`, `Windows`, `ProgramData` | 51-419 G | active | OS | KEEP |

**Loose system files (do not touch):** `pagefile.sys` 184 G, `swapfile.sys` 16 MB, `hiberfil.sys` (not visible but present).

### C:\Users\kas41 (419 G total under `Users/`) — 144 dotfiles + active dirs

**The 144 home items break into 4 cohorts.**

**Cohort A — ACTIVE, KEEP** (governance, current tools):
```
.claude (1.3 GB)        .agents (43 MB)        .chromatic (7 MB)
.harness (130 MB)       .docker (548 MB)       .config (49 MB)
.ssh (<1 MB)            .gnupg (<1 MB)         .beads (5 MB)
.bashrc / .bash_* / .env
```

**Cohort B — HEAVY ACTIVE, KEEP BUT TRIMMABLE** (IDE / runtime caches, current):
```
.vscode-insiders   8.2 GB     .cache             6.6 GB
.vscode            6.5 GB     .gemini            5.2 GB
.cursor            4.7 GB     .local             4.5 GB
.lmstudio          4.2 GB     .void-editor       3.6 GB
.rustup            3.5 GB     .bun               3.2 GB
.azurelogicapps    2.6 GB     .cargo             1.9 GB
.Neo4jDesktop2     1.0 GB     .claude            1.3 GB
.git               967 MB     .codex             588 MB
.azure-functions-core-tools 611 MB
.antigravity       776 MB
```
Total Cohort B ≈ **52 GB**. Most are cache/build artifacts that auto-regenerate. Trim selectively.

**Cohort C — ARCHIVE OR DELETE** (heavy but stale ≥3 months):
```
.continue          5.4 GB    last 2026-02-24   ARCHIVE/DELETE (single-day experiment)
.deepface          1.7 GB    last 2025-11-21   DELETE if no active face-ML work
.codeium           801 MB    last 2026-02-24   DELETE
.pearai            374 MB    last 2026-05-01   ASSESS (recent but unused?)
.sonarlint         287 MB    last 2026-01-21   DELETE if no SonarLint use
.vs-kubernetes     192 MB    last 2026-02-22   DELETE
.vibe-kanban       152 MB    last 2026-04-06   ASSESS
```

**Cohort D — ABANDONED IDE/TOOL DETRITUS** (0 MB, all mtime 2026-02-24 — single experiment day):
```
.adal .augment .cline .codebuddy .commandcode .factory .junie .iflow
.kilocode .kiro .kode .mcpjam .vibe .mux .openhands .qoder .qwen .roo
.trae .trae-cn .zencoder .neovate .pochi .openclaw .snowflake
.agentops .bob .openacp .crossnote .firecrawl .designlang .cortexide
.dbus-keyrings .ghcp-appmod .ghcp-appmod-java .image_tagger .keras
.nvidia .azad .prefect .superpowers .idlerc .gsd .sbx-denybin
.playwright-mcp .tooluniverse .pochi .qoder .pnpm-state .vscode-agents-insiders
.thumbnails .clone .semgrep .omnidesk .gk .ollama (empty) .redhat .rest-client
.streamlit .safety .minikube .poly-chromatic .azure (0.2 MB) .aws (0.2 MB)
.flowise .copilot .cagent .command_center (0.1 MB) .mcp-auth .mcp-config-backups
.tmp_learnings .ruff_cache .pip (9 MB old)
```
**~50+ near-empty dotfiles.** All bulk-deletable. Combined recovery <100 MB but huge cognitive simplification (drops home from 144 → ~50 items).

### D:\ (1031 G used) — should be sole data home

**Loose files at D:\ root (DELETE — all robocopy logs from 2026-05-23 one-time migration):**
```
robocopy_move_log.txt                    (49 MB)  ← largest
robocopy_center_mass_finalsync.log       (30 MB)
robocopy_center_mass_sync.log            (3 MB)
+ 18 more *.log / *.txt files all from 2026-05-23
models.db (zero bytes, 2026-04-24)
```

**Top-level dirs on D:\:**

| Dir | Size | mtime | Class | Action |
|-----|------|-------|-------|--------|
| `.000_AI` | **638 G** | 2026-05-26 | active AI estate | KEEP, but see split-out below |
| ↳ `.000_AI/Models` | 390 G | — | model files | KEEP |
| ↳ `.000_AI/.git` | **114 G** | — | **GIT TRACKING MODELS** | **INVESTIGATE — anti-pattern, consider migrating to LFS or removing .git** |
| ↳ `.000_AI/training` | 86 G | — | training data | KEEP |
| ↳ `.000_AI/ComfyUI` | 33 G | — | ComfyUI install | KEEP (this is the canonical per earlier audit) |
| `.101_Persona_Metadata_Atlas` | 138 G | 2026-05-25 | dataset | KEEP |
| `_archive` | **106.5 G** | 2026-05-23 | `_archive/Vids` only | **REVIEW** — old video archive, keep on cold storage or delete |
| `.20_Center_Mass` | 88 G | 2026-05-24 | active project | KEEP |
| `AI` | **69.5 G** | 2026-05-23 | `AI/.01_models` only | **INVESTIGATE — likely duplicate of `.000_AI/Models`** |
| `$Windows.~BT` | **14.87 G** | 2026-05-23 | Windows upgrade leftover | **DELETE** (Windows.old/upgrade staging — safe after upgrade complete) |
| `.04_Prism` | 13.6 G | 2026-05-25 | active project | KEEP |
| `DevTools` | 3.2 G | 2026-05-23 | dev tools | ASSESS |
| `.11_3d-Meta` | 1.7 G | 2026-05-24 | 3D asset library | KEEP |
| `Environments` | 1.1 G | 2026-05-23 | conda envs? | ASSESS |
| `Image-Org` | 1.0 G | 2026-05-24 | image organization | ASSESS |
| `3D_Meta` | 0.7 G | 2026-05-26 | **suspected dup** of `.11_3d-Meta` | **INVESTIGATE / MERGE** |
| `.archive-minimal-repos` | 0.24 G | 2026-05-23 | repo archives | KEEP (cold archive) |
| `.102_To_Scrap` | 0.07 G | 2026-05-23 | flagged scrap | **DELETE** (name says it) |
| `.07_ELECTRON_APP_WORKTREE` | 0.01 G | 2026-05-22 | git worktree | KEEP if active |
| `runtime`, `prism-autonomy-harness`, `fusion-computer`, `e`, `claude-config`, `chromatic-harness-state`, `.99_SELL` | 0 G each | — | empty stubs | **DELETE** if confirmed empty |
| `System Volume Information`, `$RECYCLE.BIN` | sys | — | OS | KEEP |

### E:\ (41 G used, 1821 G free!) — v3 landing zone

**Loose files at E:\ root:** `skill.zip` (12 KB), `Cursor WS.code-workspace` (2 KB) — minor, move/delete.

**Top-level dirs on E:\:**

| Dir | Size | Notes | Action |
|-----|------|-------|--------|
| `.00_Chromatic_Systems` | **500 MB** | Active harness root | KEEP — v3 base |
| `.01_Chromatic_Inbox_Harness` | 28.5 G | Inbox harness | KEEP |
| `.06_Prism_Autonomy_Harness` | 5.1 G | Prism autonomy | KEEP |
| `.89_Gits` | 2.9 G | Git mirrors? | ASSESS |
| `.pnpm-store` | 1.6 G | pnpm content-addressed store | KEEP (used by harness) |
| `.98_Chromatic_Brain` | 450 MB | Brain/embeddings | KEEP |
| `.12_ComftyUI-Harness` | 420 MB | ComfyUI harness | KEEP (typo: "Comfty") |
| `.71_Veil_Small_Town` | 260 MB | Veil project | KEEP |
| `sim` | 610 MB | Loose project | **MIGRATE → D:** |
| `cli-printing-press` | 200 MB | Loose tool | **MIGRATE → D:** |
| `AI_Models` | 90 MB | Tiny — probably leftover | **INVESTIGATE / DELETE** |
| `.10_AI-project-management-harness` | 30 MB | AI PM harness | KEEP |
| `.05_Chromatic_OpenRouter_Adapter` | 30 MB | OR adapter | KEEP |
| `.71_Vtest_Small_Town` | 0 MB | empty stub | **DELETE** |
| `tmp`, `New folder`, `e`, `chromatic-css` (0G), `.ruff_cache`, `.claude` (0G), `.agents` (0G) | 0 G | scratch/empty | **DELETE** |
| **Junctions to D:** `.101_Persona_Metadata_Atlas`, `.20_Center_Mass`, `.11_3d-Meta`, `AI` | — | aggregation aliases | **REVIEW** — keep for transition, retire post-v3 |

### G:\ (Google Drive)

`G:\` is Google Drive cloud sync (`My Drive`, `Other computers`, `.shortcut-targets-by-id`, `.Encrypted`). Not a local drive in the migration sense. **Out of scope** for estate cleanup; treat as external storage.

---

## §2 — Two Parallel Harness Roots (Critical v3 Issue)

`~/.claude/` and `~/.harness/` both contain governance files with overlapping content:

| File | In ~/.claude | In ~/.harness | In E:\.00_Chromatic_Systems |
|------|--------------|---------------|------------------------------|
| `AGENTS.md` | ✓ | ✓ | ✓ |
| `CLAUDE.md` | ✓ | ✓ | ✓ |
| `GLOBAL_MEMORY.md` | ✓ | ✓ | ✓ |
| `SUBAGENT_CONSTITUTION.md` | ✓ | ✓ | ✓ |
| `HARNESS_README.md` | ✓ | ✓ | ✓ |
| `AUTOCOMPACT.md` | ✓ | ✓ | — |
| `CLAUDE_SUPPORT.md` | ✓ | ✓ | — |

Three copies of governance is two too many. Per the `STANDARDS-INDEX.md` reference in CLAUDE.md, the canonical source is `E:\.00_Chromatic_Systems` (junctioned). The other two are de-facto caches / runtime overlays.

**v3 must collapse these to: E: is the source-of-truth → `~/.claude/` and `~/.harness/` become read-only junctions (or removed entirely if Claude Code/harness allow custom paths via env var).**

---

## §3 — v3 Target Architecture (Proposed)

```
C:\
├── Windows / Program Files / ProgramData          [OS — untouched]
├── Users\kas41\                                   [HOME — IDE configs, ssh, dot-runtime-state only]
│   ├── .claude\        → junction to E:\.00_Chromatic_Systems\runtime\.claude
│   ├── .harness\       → junction to E:\.00_Chromatic_Systems\runtime\.harness
│   ├── .agents\        → junction to E:\.00_Chromatic_Systems\runtime\.agents
│   ├── .chromatic\     → junction to E:\.00_Chromatic_Systems\runtime\.chromatic
│   └── (IDE state dirs — kept local, no projects)
├── Temp / pagefile.sys / swapfile.sys             [OS — untouched]
└── (no project dirs, no dotfiles for abandoned tools)

D:\
├── .000_AI\                                       [primary AI estate — Models, training, ComfyUI]
├── .04_Prism\                                     [Prism workspace — projects]
├── .20_Center_Mass\                               [Center Mass project]
├── .101_Persona_Metadata_Atlas\                   [dataset]
├── .11_3d-Meta\                                   [3D assets]
├── .07_ELECTRON_APP_WORKTREE\                     [active worktrees]
├── projects\                                      [NEW — migrated from C: (Zceleb, etc.)]
└── archive\                                       [cold storage, replaces _archive]

E:\
└── .00_Chromatic_Systems\                         [v3 harness — sole governance source]
    ├── runtime\                                   [NEW — junction targets for $HOME runtimes]
    │   ├── .claude\
    │   ├── .harness\
    │   ├── .agents\
    │   └── .chromatic\
    ├── 08_GOVERNANCE\                             [existing — keep]
    ├── 09_REGISTRY\                               [existing — keep]
    ├── 12_TEMPLATES\                              [existing — keep]
    ├── plugins\                                   [Prism plugins canonical home]
    ├── skills\                                    [Skill catalog canonical home]
    ├── hooks\                                     [Hook scripts canonical home]
    ├── v3-prep\                                   [this audit lives here]
    └── (junctions to D: project dirs for harness convenience)
```

**Boundary rules for v3:**
1. **C: hosts no projects.** Every project dir on C:\ (Zceleb, AI Inventory, VEIL, etc.) migrates or dies.
2. **D: hosts no harness state.** Every loose harness/config dir on D: (`claude-config`, `chromatic-harness-state`, `prism-autonomy-harness`, `runtime`) consolidates into E: or deletes.
3. **E: hosts no project data.** Junctions only; data lives on D:.
4. **One governance source.** E:\.00_Chromatic_Systems is canonical; `~/.claude` and `~/.harness` become managed junctions, not independent trees.
5. **No more loose files at drive roots.** Anything not a dir or system file gets a home.

---

## §4 — Phased Execution Plan (proposed — awaiting approval)

### Phase 1 — Zero-Risk Reclamation (~75 GB, ~10 min)
- Empty `C:\$Recycle.Bin` (71.82 G)
- Delete `D:\$Windows.~BT` (14.87 G — Windows upgrade leftover)
- Delete loose robocopy logs at `D:\` root (23 files, ~82 MB)
- Delete loose `tmp_*` / `Output.txt` / `DumpStack.log` / `three_stage_test_output.log` at `C:\` root
- Archive (move) the two historical migration plan MDs from `C:\` root to `E:\.00_Chromatic_Systems\v3-prep\archive\`
- Empty `C:\Temp\` contents (0.3 G)

### Phase 2 — Verified-Safe Deletions (~135 GB, ~30 min)
- Investigate + delete `C:\.02_Zceleb` (40.5 G — 5 months stale)
- Investigate + delete `C:\xttsenv` (4.5 G — 10 months stale, recreatable)
- Resolve `D:\.000_AI/.git` 114 GB anti-pattern (LFS migration or remove)
- Resolve `D:\.000_AI/Models` 390 G vs `D:\AI/.01_models` 69 G — likely 69 G dedupe
- Delete `D:\.102_To_Scrap` (0.07 G — name says it)
- Delete `D:\3D_Meta` if confirmed duplicate of `D:\.11_3d-Meta`
- Review `D:\_archive\Vids` 106 G — keep on cold storage or delete

### Phase 3 — Home Dotfile Triage (~50 GB recoverable + cognitive simplification)
- Bulk-delete Cohort D: 50+ abandoned IDE dotfiles (~100 MB but drops home from 144 → ~50 items)
- Delete Cohort C heavy stale: `.continue` (5.4 G), `.deepface` (1.7 G), `.codeium` (801 MB), `.sonarlint` (287 MB), `.vs-kubernetes` (192 MB) — pending confirmation each is unused
- Delete empty stub dirs at `C:\` root: VEIL, Zelex, root, Repositories, Dev, opal_assistant, memories, MaterialIcons, logs, datasets, c, Assets, AI-DJ, Scripts, chromatic-inbox-harness-data
- Delete empty stub dirs at `D:\` root: runtime, prism-autonomy-harness, fusion-computer, e, claude-config, chromatic-harness-state, .99_SELL
- Delete empty stub dirs at `E:\` root: tmp, New folder, e, chromatic-css (0G), .ruff_cache, .claude (0G), .agents (0G), .71_Vtest_Small_Town

### Phase 4 — Governance Consolidation (no GB freed — architectural)
- Designate `E:\.00_Chromatic_Systems` as canonical governance root (already de-facto)
- Move `E:\.00_Chromatic_Systems\runtime\` to hold the live `.claude / .harness / .agents / .chromatic` trees
- Convert `~/.claude` → junction to `E:\.00_Chromatic_Systems\runtime\.claude`
- Convert `~/.harness` → junction to `E:\.00_Chromatic_Systems\runtime\.harness`
- Convert `~/.agents` → junction to `E:\.00_Chromatic_Systems\runtime\.agents`
- Convert `~/.chromatic` → junction to `E:\.00_Chromatic_Systems\runtime\.chromatic`
- Single MEMORY.md / CLAUDE.md / AGENTS.md — others removed or junctioned
- Backup current `~/.claude` first; verify Claude Code starts cleanly with junction; rollback plan = restore from backup

### Phase 5 — v3 Scaffold + Project Migration
- Create `D:\projects\` if not present
- Migrate `C:\.02_Zceleb` → `D:\projects\Zceleb` (if kept after Phase 2 review)
- Migrate `E:\sim` → `D:\projects\sim`
- Migrate `E:\cli-printing-press` → `D:\projects\cli-printing-press`
- Audit junctions on E: → D: — retire what's redundant, keep what serves harness
- Create harness-side junctions from `E:\.00_Chromatic_Systems\projects\` → `D:\projects\` for unified browsing
- Update CLAUDE.md / AGENTS.md to reflect v3 layout
- Tag the harness repo with `v3.0.0`

---

## §5 — Investigations Required (do not act without resolving)

| Item | Question | How to resolve |
|------|----------|----------------|
| `C:\.00_AI Inventory` 19.7 G | What remains after 2026-01 partial cleanup? Models/code/dups? | `ls -R` + size by file type; cross-ref with `D:\.000_AI` |
| `D:\.000_AI/.git` 114 G | Is git tracking model files (LFS or raw)? | `git log --stat \| head -200`; check `.gitattributes` for LFS |
| `D:\AI` 69.5 G vs `D:\.000_AI/Models` 390 G | Subset, separate collection, or duplicate? | hash-compare; check if `.000_AI/Models` includes `.01_models` |
| `D:\3D_Meta` 0.7 G vs `D:\.11_3d-Meta` 1.7 G | Same data, different snapshot, or different scopes? | compare contents and mtimes |
| `D:\_archive\Vids` 106 G | Worth keeping locally vs. cold/offline? | review with user; nature of content |
| `C:\.git` (root level) | Why is there a `.git` at C:\ root? | `cat C:/.git/HEAD`, `git -C C:/ remote -v` |
| `appverifUI.dll` / `vfcompat.dll` at C:\ root | Legitimate OS files or leftover? | Microsoft signed-binary check |
| `~/.harness` vs `~/.claude` overlap | Which is canonical for which subsystem? | Diff CLAUDE.md / AGENTS.md / GLOBAL_MEMORY.md between trees; identify divergences |

---

## §6 — Risks & Rollback

1. **Recycle Bin emptying is irreversible** — 72 GB. Confirm there's nothing in there the user wants restored (`Shell.Application` namespace 0xa enumeration before purge).
2. **`$Windows.~BT` deletion** — safe if Windows upgrade is complete and stable (it appears so — mtime 2026-05-23, system has been running since).
3. **`.git` removal on `.000_AI`** — IRREVERSIBLE if history matters. Investigate first; if history is just model checkpoints, removing `.git` is fine. If it tracks code mixed with models, migrate code out before removing.
4. **Home dotfile bulk-delete** — IDE configs may contain workspace state, MRU lists, custom keybindings. Spot-check Cohort C items before purge. Cohort D (0 MB stubs) is safe.
5. **Governance junction conversion** — high-risk architectural change. Required steps:
   - Full backup of `~/.claude/`, `~/.harness/`, `~/.agents/`, `~/.chromatic/` before any move
   - Stop all Claude Code sessions
   - Move content to E: target dir
   - Create junction back to old path
   - Smoke-test: start fresh Claude Code, verify MEMORY.md loads, verify hooks fire
   - Rollback: delete junction, restore from backup
6. **Phase ordering matters.** Do not start Phase 4 (governance) until Phase 1-2 (space reclamation) is verified complete — easier to recover from a bad junction when the disks aren't full.

---

## §7 — Open Questions for Operator

1. **`.02_Zceleb`** on C:\ — active or abandoned? (mtime 2026-01-04 = 5 months stale suggests abandoned)
2. **`_archive/Vids`** 106 G on D:\ — keep, archive offsite, or delete?
3. **`.000_AI/.git`** 114 G — okay to investigate and potentially remove?
4. **`~/.harness/` directory** — what is its lifecycle? Is it the "GOL Runtime" referenced in HARNESS_README.md as a *separate executable* from Claude Code? If yes, it has independent state and consolidation needs more care.
5. **G:\ Google Drive** — confirm out-of-scope, or is the cache cleanup also wanted?
6. **Junction strategy** — okay with `~/.claude` becoming a junction to E:, or prefer to keep `~/.claude` as the canonical and junction E: → C:?
7. **Timeline** — execute phases over multiple sessions, or attempt single-day cleanup blitz?

---

**Audit complete.** No destructive actions taken. Awaiting operator decisions on §7 before proceeding to Phase 1.

— Generated: 2026-06-02 by audit-only session  
— Doc home: `E:\.00_Chromatic_Systems\v3-prep\ESTATE_AUDIT_2026-06-02.md`
