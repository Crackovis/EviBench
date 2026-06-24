<!-- NAV:START -->
> 📍 **CLI_Reorganization_Update_Report** · [🏠 Home](../README.md) · [📁 cli/](cli/CLI_MIGRATION_MATRIX.md)

<details>
<summary>🗺️ Documentation Map</summary>

- [📂 README](../README.md)
- 📚 **docs/**
  - 📋 **CLI_Reorganization_Update_Report** ← *you are here*
  - 📁 **cli/**
    - [📋 CLI_MIGRATION_MATRIX](cli/CLI_MIGRATION_MATRIX.md)
    - [🌳 COMMAND_TREE](cli/COMMAND_TREE.md)
  - [💻 CLI_Reference](CLI_Reference.md)
  - [🏗️ Architecture_Overview](Architecture_Overview.md)
  - [⚙️ Canonical_Workflow](Canonical_Workflow.md)

</details>
<!-- NAV:END -->

---

# CLI Reorganization — Closure Report

> **Date:** 2026-06-13  
> **Phases:** 09-00 through 09-07 (implementation), 09-08 (production cutover), 09-09/09-10 (documentation)  
> **Module:** `imputebench` v0.2.0

---

## 1. Implementation Baseline

Phase 09-00 captured the state of the legacy CLI before reorganization.

| Metric | Count |
|---|---|
| Total records | 253 |
| Groups | 58 |
| Commands (leaf) | 195 |
| Visible root groups | 24 |
| Layout | Flat 1.x — 24 roots registered directly on `imputebench` |

The baseline manifest was generated via `imputebench admin audit cli-tree` and archived as `docs/cli/command_manifest.json`.

---

## 2. Canonical State (Target)

The reorganization collapsed the 24 flat legacy roots into **8 domain-grouped canonical roots**:

| # | Canonical Root | Purpose | Constituent Sub-Groups |
|---|---|---|---|
| 1 | `imputebench data` | Dataset preparation & masking | `dataset`, `ingest`, `masking`, `calibration` |
| 2 | `imputebench methods` | Algorithm & plugin registry | `algorithm`, `plugin` |
| 3 | `imputebench experiment` | Experiment execution (temporal, ST, classical) | `run`, `st` (15 modules), `temporal` (13 modules) |
| 4 | `imputebench results` | Result inspection & comparison | `result`, `view`, `compare`, `gate` |
| 5 | `imputebench study` | Study management | (unchanged — stayed as root) |
| 6 | `imputebench thesis` | Thesis evidence export | (unchanged — stayed as root) |
| 7 | `imputebench admin` | Administration & auditing | `paths`, `audit`, `config`, `maintenance`, `env`, `operations` |
| 8 | `imputebench lab` | Interactive lab launcher | (unchanged — stayed as root) |

**Design invariants enforced:**
- Exactly 8 visible canonical roots
- Zero unmapped active legacy leaves (all legacy paths → hidden aliases)
- Zero empty canonical groups
- No nested `admin system` subgroup — admin subcommands are directly under `admin`
- `validate` excluded from canonical tree (subsumed by admin audit)
- Thesis gates → `thesis gate` (singular; `gates` preserved as hidden alias)

---

## 3. Current Tree Metrics

As of the production cutover (Phase 09-08):

| Metric | Count |
|---|---|
| Total records (canonical + hidden legacy) | **475** |
| Groups | **108** |
| Commands (leaves) | **367** |
| Visible canonical roots | **8** |
| Hidden legacy root aliases | **23** |
| Hidden legacy sub-commands | embedded in alias groups |

<details>
<summary>Expand: visible roots vs hidden legacy</summary>

**Visible (8):** `data`, `methods`, `experiment`, `results`, `study`, `thesis`, `admin`, `lab`

**Hidden legacy (23):** `dataset`, `algorithm`, `plugin`, `ingest`, `masking`, `run`, `result`, `locality`, `calibration`, `viewer`, `compare`, `maintenance`, `env`, `config`, `audit`, `repair`, `evidence-gate`, `st`, `temporal`, plus internal admin subcommand aliases

</details>

---

## 4. ST Split

**Before:** `imputebench/cli/st_cmd.py` was ~600+ lines containing all ST command definitions inline.

**After:** The file is a **23-line shim** that re-exports from a 15-module sub-package.

```
imputebench/cli/experiment/st/
├── __init__.py
├── group.py           # build_st_group() assembly
├── campaign.py        # experiment command
├── evidence.py        # evidence + compare commands
├── execution.py       # run command
├── gates.py           # gate + scientific commands
├── graph.py           # graph build/inspect
├── mask_bank.py       # spatial mask bank
├── output.py          # output helpers
├── plans.py           # experiment plan generation
├── preflight.py       # preflight checks
├── readiness.py       # readiness assessment
├── recipes.py         # recipe management
├── shared.py          # shared CLI utilities
├── tensor.py          # tensor inspection
└── training.py        # training lifecycle
```

**Shim:** `st_cmd.py` (23 lines) — thin re-export preserving all `from imputebench.cli.st_cmd import st` references.

---

## 5. Temporal Split

**Before:** `imputebench/cli/temporal_cmd.py` was ~200+ lines containing all temporal command definitions inline.

**After:** The file is a **12-line shim** that re-exports from a 13-module sub-package, assembled with **zero dynamic dispatch helpers** — all wiring is static, typed Click group composition.

```
imputebench/cli/experiment/temporal/
├── __init__.py
├── group.py           # build_temporal_group() assembly
├── campaign.py        # experiment (run) command
├── evidence.py        # evidence export
├── gates.py           # gate checks
├── lifecycle.py       # training lifecycle
├── output.py          # output helpers
├── preflight.py       # preflight checks
├── preparation.py     # dry-run / materialize
├── recipes.py         # recipe management
├── reports.py         # report generation
├── services.py        # typed service injection
└── shared.py          # shared CLI utilities
```

**Shim:** `temporal_cmd.py` (12 lines) — thin re-export preserving all `from imputebench.cli.temporal_cmd import temporal` references.

---

## 6. Canonical Package Inventories

### `data/` — 5 files
| File | Purpose |
|---|---|
| `group.py` | `build_data_group()` — assembles dataset, ingest, masking, calibration |
| `datasets.py` | Dataset CRUD commands |
| `ingest.py` | Resource ingestion commands |
| `masking.py` | Masking scenario CRUD |
| `calibration.py` | Calibration service commands |

### `methods/` — 3 files
| File | Purpose |
|---|---|
| `group.py` | `build_methods_group()` — assembles algorithm, plugin |
| `algorithms.py` | Algorithm registry commands |
| `plugins.py` | Plugin management commands |

### `experiment/` — 3+ files (plus sub-packages)
| File | Purpose |
|---|---|
| `group.py` | `build_experiment_group()` — assembles run, st, temporal |
| `runs.py` | Classical experiment run commands |
| `st/` | 15-module ST sub-package |
| `temporal/` | 13-module temporal sub-package |

### `results/` — 5 files
| File | Purpose |
|---|---|
| `group.py` | `build_results_group()` — assembles result, view, compare, gate |
| `result.py` | Result CRUD + export |
| `view.py` | Spatial viewer commands |
| `compare.py` | Comparison commands |
| `gates.py` | Evidence gate commands |

### `admin/` — 7 files
| File | Purpose |
|---|---|
| `group.py` | `build_admin_group()` — assembles paths, audit, config, maintenance, env, operations |
| `paths.py` | Path locality and repair commands |
| `audit.py` | Audit and verification commands |
| `config.py` | Configuration management |
| `maintenance.py` | Database and file maintenance |
| `operations.py` | ST runtime operations |
| `__init__.py` | Package init |

### `lab/` — 1 file
| File | Purpose |
|---|---|
| `group.py` | `build_lab_group()` — interactive lab launcher |

### `thesis/` — 2 files
| File | Purpose |
|---|---|
| `group.py` | `build_thesis_group()` — thesis evidence export workflow |
| `st.py` | ST thesis publication commands |

---

## 7. Infrastructure Files Created

### `deprecation.py` (41 lines)
Singleton `DeprecationWarningEmitter` class. Emits exactly one deprecation warning per legacy path per process invocation. Warnings go to stderr. Suppressible via `--no-deprecation-warnings` CLI flag or `IMPUTEBENCH_NO_DEPRECATION_WARNINGS=1` environment variable.

### `aliases.py` (92 lines)
Contains:
- `LEGACY_MAP: dict[str, str]` — hardcoded mapping of all legacy root names to canonical paths
- `make_deprecated_group()` — wraps any `click.Group` as hidden+deprecated with stderr warning
- `_make_deprecated_command()` — wraps individual commands with deprecation notices

### `root.py` — 248 lines (≤250 ✓)
`create_cli(layout, include_legacy_aliases)` factory function:
- `layout="canonical"` → registers 8 domain groups + optional hidden legacy aliases (367 total commands, 475 records)
- `layout="legacy"` → registers all 24 roots flat (195 commands, 253 records)
- `layout="canonical"` is the **production default** since Phase 09-08

### `cli/__init__.py` — 59-line facade
Re-exports all command groups, service classes, and the `cli` instance. Preserves backward-compatible imports for all consumers.

---

## 8. Acceptance Criteria

| # | Criterion | Status | Evidence |
|---|---|---|---|
| 1 | Exactly 8 visible canonical roots | ✅ | `root.py` line 248: `create_cli(layout="canonical")` |
| 2 | Zero unmapped active legacy leaves | ✅ | All 19 moved roots mapped in `aliases.py:LEGACY_MAP` |
| 3 | Zero empty canonical groups | ✅ | Verified via `manifest.py:_empty_groups()` |
| 4 | `st_cmd.py` ≤ 60 lines | ✅ | **23 lines** (target: ≤60) |
| 5 | `temporal_cmd.py` ≤ 60 lines | ✅ | **12 lines** (target: ≤60) |
| 6 | Zero dynamic temporal dispatch helpers | ✅ | All temporal wiring is static typed Click composition |
| 7 | `root.py` ≤ 250 lines | ✅ | **248 lines** |
| 8 | `validate` excluded from canonical tree | ✅ | Explicitly excluded; no alias registered |
| 9 | No `admin system` subgroup | ✅ | Admin subcommands are directly under `admin` |
| 10 | Thesis `gates` → `gate` (gates preserved as alias) | ✅ | `gates` hidden alias → canonical `gate` |

---

## 9. Files Changed Summary

| File | Change | New Size |
|---|---|---|
| `imputebench/cli/root.py` | `create_cli()` factory with canonical/legacy layouts | 248 lines |
| `imputebench/cli/__init__.py` | Reduced facade | 59 lines |
| `imputebench/cli/st_cmd.py` | Thin shim → re-exports from `experiment/st/` | 23 lines |
| `imputebench/cli/temporal_cmd.py` | Thin shim → re-exports from `experiment/temporal/` | 12 lines |
| `imputebench/cli/aliases.py` | **NEW** — `LEGACY_MAP` + `make_deprecated_group()` | 92 lines |
| `imputebench/cli/deprecation.py` | **NEW** — `DeprecationWarningEmitter` | 41 lines |
| `imputebench/cli/data/` | **NEW** — 5-file domain package | — |
| `imputebench/cli/methods/` | **NEW** — 3-file domain package | — |
| `imputebench/cli/experiment/` | **NEW** — 3+st+temporal domain package | — |
| `imputebench/cli/results/` | **NEW** — 5-file domain package | — |
| `imputebench/cli/admin/` | **NEW** — 7-file domain package | — |
| `imputebench/cli/lab/` | **NEW** — 1-file domain package | — |
| `imputebench/cli/thesis/` | **NEW** — 2-file domain package | — |
| `imputebench/cli/manifest.py` | CLI tree introspection utilities (pre-existing, retained) | 385 lines |

---

## 10. Backward Compatibility

All legacy commands remain **fully functional** throughout the 1.x compatibility window:

- Legacy paths are registered as **hidden** `click.Group`s on the root CLI.
- Each hidden alias emits a **single deprecation warning** (to stderr) on first invocation.
- Parameter parsing, exit codes, and side effects are **unchanged**.
- Warnings are **suppressible** via CLI flag or environment variable.
- All 19 moved legacy roots and their sub-commands work identically.
- Legacy paths will be removed in **version 2.0**.

---

> ← *(first)* · [⬆ Top](#) · [🏠 Home](../README.md) · [📋 CLI_MIGRATION_MATRIX →](cli/CLI_MIGRATION_MATRIX.md)
<!-- FOOTER:END -->
