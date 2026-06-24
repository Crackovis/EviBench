# CLI Completeness Audit — Sprint 06 Baseline

**Generated:** 2026-06-13  
**Repository:** `Interfaces/EviBench/`  
**Branch:** `master`  
**Audit scope:** All CLI commands, service-layer support, progress framework, CRUD gaps.

---

## 1. Codebase Structure Overview

```
imputebench/
├── cli/                          # 34 CLI command files (Click-based)
│   ├── __init__.py               # Root CLI app, plugin_loader builder
│   ├── progress_options.py       # Shared --progress decorator + config resolver
│   ├── runs_cmd.py               # run create/execute/status (114 lines)
│   ├── temporal_cmd.py           # Full temporal orchestration CLI (597 lines)
│   ├── st_cmd.py                 # Full ST orchestration CLI (2793 lines)
│   ├── thesis_all_cmd.py         # Thesis evidence pack orchestrator (755 lines)
│   ├── compare_cmd.py            # ALL DECOMMISSIONED (73 lines)
│   ├── plugins_cmd.py            # list/validate/register/scaffold (116 lines)
│   ├── datasets_cmd.py           # list/register/show/delete (92 lines)
│   ├── algorithms_cmd.py         # list/list-builtin/register/show/delete (107 lines)
│   ├── masking_cmd.py            # list/create/show/delete (89 lines)
│   ├── study_cmd.py              # study group with arma-order/stationarity/acf-pacf
│   ├── admin_cmd.py, audit_cmd.py, calibration_cmd.py, config_cmd.py
│   ├── env_cmd.py, evidence_gate_cmd.py, ingest_cmd.py, lab_cmd.py
│   ├── locality_cmd.py, maintenance_cmd.py, repair_cmd.py
│   ├── results_cmd.py, validate_cmd.py, viewers_cmd.py
│   └── thesis_*.py (6 thesis subcommands)
├── services/
│   ├── progress/                 # Universal progress framework (COMPLETE)
│   │   ├── __init__.py           # Public API surface
│   │   ├── progress_config.py    # ProgressConfig (frozen dataclass)
│   │   ├── progress_events.py    # ProgressEvent + factory helpers (243 lines)
│   │   ├── progress_manager.py   # TTY/CI detection, backend resolution (166 lines)
│   │   └── progress_reporter.py  # ProgressReporter ABC + implementations (650 lines)
│   ├── run_service.py            # Full CRUD: list/get/save/update/delete/purge (289 lines)
│   ├── comparison/
│   │   ├── studio_manager_service.py  # Full CRUD for ComparisonSpec (361 lines)
│   │   ├── studio_service.py     # StudioComparisonService
│   │   └── persistence.py        # load_spec/save_spec/list_specs/delete_spec
│   ├── dataset_service.py        # register/update/list/get/get_or_none/delete (271 lines)
│   ├── algorithm_service.py      # register/update/list/get/delete (175 lines)
│   ├── masking_service.py        # register/update/list/get/get_or_none/delete (230 lines)
│   ├── plugin_loader_service.py  # Plugin discovery, validation, registration
│   ├── temporal/                 # 8 temporal services
│   └── spatiotemporal/           # ST orchestrator + services
└── tests/
    ├── cli/                      # CLI-focused tests
    ├── progress/                 # Progress framework tests
    ├── temporal/                 # Temporal domain tests
    └── spatiotemporal/           # ST domain tests
```

---

## 2. Progress Framework — Current State

### 2.1 Framework Components (ALL EXIST)

| Component | File | Status |
|---|---|---|
| `ProgressConfig` | `progress/progress_config.py` | COMPLETE |
| `ProgressEvent` + factories | `progress/progress_events.py` | COMPLETE |
| `ProgressManager` | `progress/progress_manager.py` | COMPLETE |
| `ProgressReporter` (ABC) | `progress/progress_reporter.py` | COMPLETE |
| `ProgressContext` | `progress/progress_reporter.py` | COMPLETE |
| `SilentProgressReporter` | `progress/progress_reporter.py` | COMPLETE |
| `TqdmProgressReporter` | `progress/progress_reporter.py` | COMPLETE |
| `RichProgressReporter` | `progress/progress_reporter.py` | COMPLETE |
| `JsonEventLogReporter` | `progress/progress_reporter.py` | COMPLETE |
| `CompositeProgressReporter` | `progress/progress_reporter.py` | COMPLETE |
| `progress_options` decorator | `cli/progress_options.py` | COMPLETE |
| `resolve_progress_config()` | `cli/progress_options.py` | COMPLETE |

### 2.2 CLI Root Options (EXIST)

```text
--progress / --no-progress
--progress-backend [auto|tqdm|rich|silent]
--progress-event-log PATH
```

### 2.3 Currently Integrated Commands

| Command | Status |
|---|---|
| `study temporal arma-order` | INTEGRATED (Sheet 05) |
| `study temporal stationarity` | INTEGRATED (Sheet 05) |
| `study temporal acf-pacf` | INTEGRATED (Sheet 05) |

### 2.4 Deferred Commands (from Sheet 05)

| Command | Priority | Status |
|---|---|---|
| `temporal experiment run` | P0 | NOT WIRED |
| `temporal prepare materialize` | P1 | NOT WIRED |
| `st experiment run` | P2 | NOT WIRED |
| `thesis all` | P3 | NOT WIRED |

---

## 3. Run CLI — Current State

### 3.1 Existing Commands

| Command | Implementation | Status |
|---|---|---|
| `run create` | Direct `ExperimentRunner.create_run()` | WORKING |
| `run execute RUN_ID` | Direct `ExperimentRunner.execute()` | WORKING |
| `run status RUN_ID` | Direct `ExperimentRunner.get_run()` | WORKING |

### 3.2 RunService API (ALL EXIST)

| Method | Signature | Cascade Behavior |
|---|---|---|
| `list()` | `-> list[ExperimentRun]` | SQLite read |
| `get(run_id)` | `-> ExperimentRun` | SQLite read |
| `get_or_none(run_id)` | `-> ExperimentRun \| None` | SQLite read |
| `exists(run_id)` | `-> bool` | SQLite read |
| `save(run)` | `-> ExperimentRun` | SQLite write |
| `update(run)` | `-> ExperimentRun` | SQLite write |
| `patch_status(run_id, ...)` | `-> ExperimentRun` | SQLite write |
| `delete(run_id)` | `-> bool` | **FULL CASCADE**: results + artifacts + timing + catalog |
| `purge(run_id)` | `-> bool` | Results + artifacts only; keeps run record |

### 3.3 Missing Commands

| Command | Service Support | Gap |
|---|---|---|
| `run list` | `RunService.list()` exists | No CLI handler, no query filtering |
| `run show RUN_ID` | `RunService.get()` exists | No CLI handler, no detail formatting |
| `run delete RUN_ID` | `RunService.delete()` exists (FULL cascade) | No CLI handler, no impact preview, no --no-cascade |

### 3.4 Critical Note

`RunService.delete()` is already a **full cascade**: removes the run record, all linked `Result` objects, artifact files/directories, result-store directories, timing spans, and owned catalog rows. The `run delete --no-cascade` option must NOT call this method.

---

## 4. Compare CLI — Current State

### 4.1 Existing Commands: ALL DECOMMISSIONED

| Command | Status |
|---|---|
| `compare create` | `raise ClickException("Legacy comparison CLI has been decommissioned")` |
| `compare show` | `raise ClickException(...)` |
| `compare export` | `raise ClickException(...)` |
| `compare export-latex` | `raise ClickException(...)` |

### 4.2 Available Service Support

**StudioManagerService** (`services/comparison/studio_manager_service.py`, 361 lines) provides:

| Method | Description |
|---|---|
| `list_active_specs()` | List non-archived ComparisonSpec objects |
| `list_archived_specs()` | List archived ComparisonSpec objects |
| `list_all_specs()` | List all ComparisonSpec objects |
| `rename_spec(spec_id, name)` | Rename a comparison plan |
| `duplicate_spec(spec_id)` | Deep-copy a comparison plan |
| `update_notes(spec_id, notes)` | Update notes |
| `archive_spec(spec_id)` / `unarchive_spec(spec_id)` | Archive/unarchive |
| `mark_stale(spec_id)` | Mark as stale |
| `repair_stale(spec_id)` | Repair stale references |
| `delete_spec(spec_id)` | Delete a ComparisonSpec (NOT results) |

**Persistence layer** (`services/comparison/persistence.py`):
- `load_spec()` / `save_spec()` / `list_specs()` / `delete_spec()`

### 4.3 Gap Analysis

| Command | Needs |
|---|---|
| `compare list` | New: CLI handler using `StudioManagerService.list_active_specs()` |
| `compare show COMPARISON_ID` | New: CLI handler using `load_spec()` |
| `compare delete COMPARISON_ID` | New: CLI handler using `StudioManagerService.delete_spec()` |

---

## 5. Plugin CLI — Current State

### 5.1 Existing Commands

| Command | Status |
|---|---|
| `plugin list` | WORKING — uses `_build_plugin_loader().discover()` |
| `plugin validate SLUG` | WORKING |
| `plugin register SLUG` | WORKING |
| `plugin scaffold SLUG` | WORKING |

### 5.2 Missing Commands

| Command | Service Support | Gap |
|---|---|---|
| `plugin show SLUG` | `PluginLoaderService` exists; needs `inspect()` method | No CLI handler, no detail payload |
| `plugin delete SLUG` | No lifecycle service exists | Requires `PluginLifecycleService` with: canonical-root check, built-in refusal, active dependency detection, quarantine semantics |

### 5.3 Plugin Loader Service Analysis

`PluginLoaderService` (referenced via `cli/__init__.py._build_plugin_loader()`):
- `discover()` — yields (plugin_dir, manifest)
- `validate(plugin_dir)` — returns validation result
- `register(plugin_dir)` — registers as algorithm
- `scaffold(slug, name, family, target_dir)` — creates plugin skeleton

Missing:
- `inspect(slug)` — returns manifest, validation, dependencies, registration status
- No deletion/quarantine capability

---

## 6. Entity CRUD — Current State

### 6.1 Dataset CLI

| Command | Status | Service Support |
|---|---|---|
| `dataset list` | WORKING | `DatasetService.list()` |
| `dataset register` | WORKING | `DatasetService.register()` |
| `dataset show ID` | WORKING | `DatasetService.get()` |
| `dataset delete ID` | WORKING | `DatasetService.delete()` |
| `dataset update ID` | **MISSING** | `DatasetService.update()` EXISTS |

### 6.2 Algorithm CLI

| Command | Status | Service Support |
|---|---|---|
| `algorithm list` | WORKING | `AlgorithmService.list()` |
| `algorithm list-builtin` | WORKING | CLI-level constant |
| `algorithm register` | WORKING | `AlgorithmService.register()` |
| `algorithm show ID` | WORKING | `AlgorithmService.get()` |
| `algorithm delete ID` | WORKING | `AlgorithmService.delete()` |
| `algorithm update ID` | **MISSING** | `AlgorithmService.update()` EXISTS |

### 6.3 Masking CLI

| Command | Status | Service Support |
|---|---|---|
| `masking list` | WORKING | `MaskingService.list()` |
| `masking create` | WORKING | `MaskingService.register()` |
| `masking show ID` | WORKING | `MaskingService.get()` |
| `masking delete ID` | WORKING | `MaskingService.delete()` |
| `masking update ID` | **MISSING** | `MaskingService.update()` EXISTS |

---

## 7. Temporal CLI — Current State

### 7.1 Command Inventory (597 lines)

| Group | Commands | Lines |
|---|---|---|
| `temporal recipe` | list, inspect, algorithms, masks, tiers, validate | ~290 |
| `temporal prepare` | dry-run, materialize, verify | ~40 |
| `temporal experiment` | dry-run, materialize, run, status, reset, phase, certify | ~170 |
| `temporal evidence` | export, inspect | ~50 |
| `temporal gate` | list, run, report | ~75 |
| `temporal preflight` | run, study | ~50 |
| `temporal lifecycle` | policies, inspect, report | ~60 |
| `temporal report` | readme, summary | ~60 |

### 7.2 Progress Wiring Status

| Command | Framework Integration | Status |
|---|---|---|
| All temporal commands | None | **NO PROGRESS WIRING** |
| `temporal experiment run` | None | PRIORITY P0 |
| `temporal prepare materialize` | None | PRIORITY P1 |

### 7.3 Temporal Services (All Exist)

- `TemporalRecipeBookService`
- `TemporalPreparationService`
- `TemporalExperimentOrchestrator` (has `TemporalProgressService`, `TemporalEventLogService`, `TemporalExecutionLedgerService`)
- `TemporalEvidenceExportService`
- `TemporalGateService`
- `TemporalPreflightService`
- `TemporalLifecycleService`
- `TemporalReportService`

---

## 8. ST CLI — Current State

### 8.1 Command Inventory (2793 lines)

| Group | Commands |
|---|---|
| `st graph` | build, inspect |
| `st tensor` | inspect |
| `st mask-bank` | create (ST03) |
| `st plan` | create (ST05) |
| `st run` | execute |
| `st compare` | (ST06+) |
| `st evidence` | export, inspect |
| `st gate` | list, run, report |
| `st preflight` | run |
| `st recipe` | list, inspect, algorithms, masks, tiers, validate |
| `st recipe prepare` | dry-run, materialize, verify |
| `st recipe experiment` | dry-run, materialize, run, status, reset |
| `st report` | readme, summary |
| `st chapter06` | mirror, report |

### 8.2 Progress Wiring Status

| Command | Status |
|---|---|
| All ST commands | **NO PROGRESS WIRING** |
| `st recipe experiment run` | PRIORITY P2 |

---

## 9. Thesis CLI — Current State

### 9.1 Command Structure (755 lines)

`thesis all` runs 10 evidence packs sequentially:

```text
00_training        — audit snapshot + training lifecycle
01_dataset         — dataset characterization figures
02_missingness     — missingness characterization figures
03_training_ev     — training-curve plots
04_algorithm_cards — algorithm inventory + lifecycle
05_comparison_tbl  — algorithm/family comparison tables
06_sensitivity     — masking-sensitivity curves
07_pollutants      — per-pollutant breakdown
08_compare         — official comparison figures
09_gates           — thesis evidence readiness gate
```

### 9.2 Progress Wiring Status

| Command | Status |
|---|---|
| `thesis all` | **NO PROGRESS WIRING** (PRIORITY P3) |

---

## 10. Secondary Gap Assessment

### 10.1 Classification by Readiness

#### SAFE NOW (service support exists, CLI handler needed)

| Command | Service | Notes |
|---|---|---|
| `run list` | `RunService.list()` | Needs `RunQueryService` for filtering |
| `run show` | `RunService.get()` | Needs detail formatting |
| `run delete` | `RunService.delete()` | Needs impact model, --no-cascade |
| `plugin show` | `PluginLoaderService` | Needs `inspect()` method |
| `compare list` | `StudioManagerService.list_active_specs()` | Replace legacy CLI |
| `compare show` | `load_spec()` from persistence | Replace legacy CLI |
| `compare delete` | `StudioManagerService.delete_spec()` | Replace legacy CLI |
| `dataset update` | `DatasetService.update()` | Patch semantics needed |
| `algorithm update` | `AlgorithmService.update()` | Patch semantics needed |
| `masking update` | `MaskingService.update()` | Patch semantics needed |

#### REQUIRES FOUNDATION (infrastructure gap before CLI)

| Command | Missing Foundation |
|---|---|
| `plugin delete` | No `PluginLifecycleService` — needs canonical-root checks, active dependency detection, quarantine |
| `lab list` | No `LabSession` model — needs PID/port/start-time registry |
| `lab stop` | No managed session persistence — needs process identity validation |
| `admin backup` | No `AdminBackupService` — needs metadata-db + config backup |
| `config set` | No typed canonical config service/schema |
| `result copy` | No `ResultCloneService` — needs clone semantics, provenance |
| `ingest list` | No canonical ingestion-history contract |

#### DEFERRED (not in scope for this sprint)

| Command | Reason |
|---|---|
| `audit list` | Audit subsystem needs canonical registry first |
| `admin status` | Safe read-only; lower priority |

---

## 11. Test Infrastructure

### 11.1 Test Directory Structure (394 test files)

| Directory | Test Count (approx.) |
|---|---|
| `tests/cli/` | ~30 files |
| `tests/progress/` | ~10 files |
| `tests/temporal/` | ~15 files |
| `tests/spatiotemporal/` | ~20 files |
| `tests/services/` | ~40 files |
| Root-level tests | ~200 files |

### 11.2 Test Templates for New Work

Existing patterns:
- CLI tests use `click.testing.CliRunner`
- Service tests mock repositories
- Progress tests verify event emission and silent mode

---

## 12. Risk Register

| Risk | Severity | Findings |
|---|---|---|
| `--no-cascade` invokes full deletion | CRITICAL | `RunService.delete()` is full cascade. Must create separate `delete_record_only()` |
| Plugin delete removes arbitrary path | CRITICAL | Must implement canonical-root containment + quarantine |
| Compare CLI uses legacy persistence | HIGH | Legacy CLI raises exceptions. `StudioManagerService` is the canonical path |
| Progress duplicates domain events | MEDIUM | Universal reporter must be additive adapter |
| Progress corrupts JSON stdout | MEDIUM | Progress → stderr; JSON → stdout (already designed) |
| ST progress changes scientific outputs | CRITICAL | Must preserve plan/run/fingerprint/gate identity |
| Update commands clear unspecified fields | HIGH | Must patch only supplied fields |
| Lab stop kills unrelated PID | CRITICAL | Deferred until managed session registry exists |
| Generic config set corrupts files | HIGH | Deferred until typed config service exists |

---

## 13. Summary Matrix

### 13.1 Commands to Add

| # | Command | Patch | Cycle |
|---|---|---|---|
| 1 | `run list` | 06B-01 | B |
| 2 | `run show` | 06B-02 | B |
| 3 | `run delete` | 06B-03 | B |
| 4 | `plugin show` | 06B-04 | B |
| 5 | `plugin delete` | 06B-05 | B |
| 6 | `compare list` | 06B-06 | B |
| 7 | `compare show` | 06B-06 | B |
| 8 | `compare delete` | 06B-06 | B |
| 9 | `dataset update` | 06B-07 | B |
| 10 | `algorithm update` | 06B-07 | B |
| 11 | `masking update` | 06B-07 | B |

### 13.2 Progress Wiring Targets

| # | Command | Patch | Cycle |
|---|---|---|---|
| 1 | `temporal experiment run` | 06A-01 | A |
| 2 | `temporal prepare materialize` | 06A-02 | A |
| 3 | `st experiment run` | 06A-03 | A |
| 4 | `thesis all` | 06A-04 | A |
| 5 | Remaining long ops | 06A-05 | A |

### 13.3 New Service Files Needed

| File | Purpose |
|---|---|
| `services/run_query_service.py` | Query/filter/format for run list/show |
| `services/plugin_lifecycle_service.py` | Safe plugin delete with quarantine |
| `read_models/cli/run_details.py` | Run detail DTO |
| `read_models/cli/delete_impact.py` | Run delete impact DTO |

---

## 14. Branch State

- **Active branch:** `master`
- **Recent commits:** Sheets 03-05 implementation, temporal orchestration, ST CLI philosophy
- **Preconditions met:** Sheets 03, 04, 05 are implemented

---

*End of audit.*
