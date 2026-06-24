<!-- NAV:START -->
> 📍 **CLI_MIGRATION_MATRIX** · [🏠 Home](../../README.md) · [📁 cli/](COMMAND_TREE.md)

<details>
<summary>🗺️ Documentation Map</summary>

- [📂 README](../../README.md)
- 📚 **docs/**
  - 📁 **cli/**
    - 📋 **CLI_MIGRATION_MATRIX** ← *you are here*
    - [🌳 COMMAND_TREE](COMMAND_TREE.md)
    - [📊 command_manifest](command_manifest.json)
  - [💻 CLI_Reference](../CLI_Reference.md)
  - [📝 CLI_Reorganization_Update_Report](../CLI_Reorganization_Update_Report.md)
  - [🏗️ Architecture_Overview](../Architecture_Overview.md)
  - [⚙️ Canonical_Workflow](../Canonical_Workflow.md)
  - [📖 Introduction](../Introduction.md)

</details>
<!-- NAV:END -->

---

# CLI Migration Matrix — Legacy → Canonical

> **Cutover date:** 2026-06-13  
> **Version:** imputebench 0.2.0  
> **Compatibility window:** All legacy paths remain available as hidden aliases through 1.x

---

## Root Command Mapping

Every top-level legacy root command maps to a canonical 8-domain position.

| # | Legacy Path (`imputebench …`) | Canonical Path (`imputebench …`) | Status |
|---|---|---|---|
| 1 | `dataset` | `data dataset` | Moved |
| 2 | `algorithm` | `methods algorithm` | Moved |
| 3 | `plugin` | `methods plugin` | Moved |
| 4 | `ingest` | `data ingest` | Moved |
| 5 | `masking` | `data masking` | Moved |
| 6 | `run` | `experiment run` | Moved |
| 7 | `result` | `results result` | Moved |
| 8 | `locality` | `admin paths` | Moved |
| 9 | `calibration` | `data calibration` | Moved |
| 10 | `viewer` | `results view` | Moved |
| 11 | `compare` | `results compare` | Moved |
| 12 | `maintenance` | `admin maintenance` | Moved |
| 13 | `env` | `admin env` | Moved |
| 14 | `config` | `admin config` | Moved |
| 15 | `admin` | `admin` | Unchanged |
| 16 | `audit` | `admin audit` | Moved |
| 17 | `repair` | `admin paths` | Moved |
| 18 | `evidence-gate` | `results gate` | Moved |
| 19 | `st` | `experiment st` | Moved |
| 20 | `temporal` | `experiment temporal` | Moved |
| 21 | `study` | `study` | Unchanged |
| 22 | `thesis` | `thesis` | Unchanged |
| 23 | `lab` | `lab` | Unchanged |
| 24 | `validate` | — | **Removed** |
| 25 | — (Python builders) | `imputebench experiment recipe` | **New** |

---

## Canonical 8-Domain Architecture

After reorganization the CLI presents **exactly 8 visible roots**:

| Domain | Canonical Command | Contents |
|---|---|---|
| **data** | `imputebench data` | `dataset`, `ingest`, `masking`, `calibration` |
| **methods** | `imputebench methods` | `algorithm`, `plugin` |
| **experiment** | `imputebench experiment` | `run`, `recipe` (12 subcommands), `st` (15 modules), `temporal` (13 modules) |
| **results** | `imputebench results` | `result`, `view`, `compare`, `gate` |
| **study** | `imputebench study` | (unchanged) |
| **thesis** | `imputebench thesis` | `dataset`, `missingness`, `training`, `algorithms`, `compare`, `gates`, `all`, plus ST thesis commands |
| **admin** | `imputebench admin` | `paths`, `audit`, `config`, `maintenance`, `env`, `operations` |
| **lab** | `imputebench lab` | `start` |

---

## Domain-Package File Counts

| Canonical Package | Source Location | Files |
|---|---|---|
| `data` | `imputebench/cli/data/` | `group.py`, `datasets.py`, `ingest.py`, `masking.py`, `calibration.py` (5) |
| `methods` | `imputebench/cli/methods/` | `group.py`, `algorithms.py`, `plugins.py` (3) |
| `experiment` | `imputebench/cli/experiment/` | `group.py`, `runs.py`, `recipes.py`, plus `st/` (15 modules), `temporal/` (13 modules) |
| `results` | `imputebench/cli/results/` | `group.py`, `result.py`, `view.py`, `compare.py`, `gates.py` (5) |
| `admin` | `imputebench/cli/admin/` | `group.py`, `paths.py`, `audit.py`, `config.py`, `maintenance.py`, `operations.py`, `__init__.py` (7) |
| `lab` | `imputebench/cli/lab/` | `group.py` (1) |
| `thesis` | `imputebench/cli/thesis/` | `group.py`, `st.py` (2) |

---

## ST Sub-Command Mapping

The `st` group moved from `imputebench st` to `imputebench experiment st`. All sub-commands preserved.

| Legacy Path | Canonical Path | Module |
|---|---|---|
| `imputebench st graph` | `imputebench experiment st graph` | `experiment/st/graph.py` |
| `imputebench st mask-bank` | `imputebench experiment st mask-bank` | `experiment/st/mask_bank.py` |
| `imputebench st tensor` | `imputebench experiment st tensor` | `experiment/st/tensor.py` |
| `imputebench st recipe` | `imputebench experiment st recipe` | `experiment/st/recipes.py` |
| `imputebench st plan` | `imputebench experiment st plan` | `experiment/st/plans.py` |
| `imputebench st experiment` | `imputebench experiment st experiment` | `experiment/st/campaign.py` |
| `imputebench st run` | `imputebench experiment st run` | `experiment/st/execution.py` |
| `imputebench st compare` | `imputebench experiment st compare` | `experiment/st/evidence.py` |
| `imputebench st evidence` | `imputebench experiment st evidence` | `experiment/st/evidence.py` |
| `imputebench st gate` | `imputebench experiment st gate` | `experiment/st/gates.py` |
| `imputebench st scientific` | `imputebench experiment st scientific` | `experiment/st/gates.py` |
| `imputebench st preflight` | `imputebench experiment st preflight` | `experiment/st/preflight.py` |
| `imputebench st readiness` | `imputebench experiment st readiness` | `experiment/st/readiness.py` |
| `imputebench st training` | `imputebench experiment st training` | `experiment/st/training.py` |
| `imputebench st audit` | `imputebench experiment st audit` | `admin/audit.py` |
| `imputebench st env` | `imputebench experiment st env` | `admin/operations.py` |

**ST package:** 15 modules in `imputebench/cli/experiment/st/`:
`campaign.py`, `evidence.py`, `execution.py`, `gates.py`, `graph.py`, `group.py`, `mask_bank.py`, `output.py`, `plans.py`, `preflight.py`, `readiness.py`, `recipes.py`, `shared.py`, `tensor.py`, `training.py`

**Shim:** `imputebench/cli/st_cmd.py` reduced to **23 lines** (was ~600+).

---

## Temporal Sub-Command Mapping

The `temporal` group moved from `imputebench temporal` to `imputebench experiment temporal`. All sub-commands preserved via static typed wiring — **zero dynamic dispatch helpers**.

| Legacy Path | Canonical Path | Module |
|---|---|---|
| `imputebench temporal recipe` | `imputebench experiment temporal recipe` | `experiment/temporal/recipes.py` |
| `imputebench temporal prepare` | `imputebench experiment temporal prepare` | `experiment/temporal/preparation.py` |
| `imputebench temporal experiment` | `imputebench experiment temporal experiment` | `experiment/temporal/campaign.py` |
| `imputebench temporal preflight` | `imputebench experiment temporal preflight` | `experiment/temporal/preflight.py` |
| `imputebench temporal lifecycle` | `imputebench experiment temporal lifecycle` | `experiment/temporal/lifecycle.py` |
| `imputebench temporal evidence` | `imputebench experiment temporal evidence` | `experiment/temporal/evidence.py` |
| `imputebench temporal gate` | `imputebench experiment temporal gate` | `experiment/temporal/gates.py` |
| `imputebench temporal report` | `imputebench experiment temporal report` | `experiment/temporal/reports.py` |

**Temporal package:** 13 modules in `imputebench/cli/experiment/temporal/`:
`campaign.py`, `evidence.py`, `gates.py`, `group.py`, `lifecycle.py`, `output.py`, `preflight.py`, `preparation.py`, `recipes.py`, `reports.py`, `services.py`, `shared.py`, `__init__.py`

**Shim:** `imputebench/cli/temporal_cmd.py` reduced to **12 lines** (was ~200+).

---

## New Commands (v0.3.0)

| # | Canonical Path | Old Status | Status | Notes |
|---|---|---|---|---|
| 1 | `imputebench experiment recipe` | Python builder functions, no CLI | **New** | 12 subcommands: list, show, create, clone, update, delete, validate, export, history, materialize, entry (5), algorithm (2). Built-in books migrated to SQLite recipe registry via `admin migrate recipe-books`. See [Recipe_Book_Architecture.md](../Recipe_Book_Architecture.md). |

---

## Removed Commands

| Legacy Path | Disposition | Notes |
|---|---|---|
| `imputebench validate` | Removed | Validation functionality was subsumed into admin audit checks; no replacement command. |

---

## Infrastructure Files

| File | Lines | Role |
|---|---|---|
| `imputebench/cli/root.py` | 248 | `create_cli()` factory — canonical + legacy layout |
| `imputebench/cli/__init__.py` | 59 | Facade re-exporting all commands + services |
| `imputebench/cli/aliases.py` | 92 | `LEGACY_MAP` dict + `make_deprecated_group()` |
| `imputebench/cli/deprecation.py` | 41 | `DeprecationWarningEmitter` — one warning per path per invocation |
| `imputebench/cli/manifest.py` | 385 | `traverse_click_tree()`, `generate_manifest()`, `build_audit_cli_tree_command()` |
| `imputebench/cli/st_cmd.py` | 23 | Thin re-export shim for `experiment/st/` |
| `imputebench/cli/temporal_cmd.py` | 12 | Thin re-export shim for `experiment/temporal/` |

---

## Deprecation Behavior

- Legacy paths remain **fully functional** — they emit a single stderr deprecation warning on first invocation.
- Warnings are suppressible via `--no-deprecation-warnings` or `IMPUTEBENCH_NO_DEPRECATION_WARNINGS=1`.
- Legacy paths are marked `hidden=True` and excluded from `--help` output, completion, and canonical tree listings.
- All legacy paths will be removed in **2.0**.

---

> ← *(first)* · [⬆ Top](#) · [🏠 Home](../../README.md) · [📁 COMMAND_TREE →](COMMAND_TREE.md)
<!-- FOOTER:END -->
