# CLI Completeness Sprint Update Report

**Generated:** 2026-06-13  
**Repository:** `Interfaces/EviBench/`  
**Scope:** ImputeBench CLI completeness sprint closure: CRUD parity, progress integration, safety decisions, tests, and backward compatibility.

---

## 1. Audit Summary

The sprint started from `docs/CLI_Completeness_Audit.md`, which found that the core CLI was broad but uneven: run, plugin, comparison, dataset, algorithm, and masking services already exposed enough service-layer capability for additional CLI handlers, while long-running temporal/ST/thesis commands lacked the universal progress framework wiring. The baseline audit also highlighted destructive-action risks: `RunService.delete()` was already a full cascade, plugin deletion needed canonical-root/quarantine safeguards, comparison deletion must not remove results or artifacts, and update commands must patch only supplied fields.

`docs/CLI_Completeness_P3_Audit.md` classified secondary gaps after the main sprint scope. It refined `admin status` as `READY` if kept read-only, and kept `lab list`, `lab stop`, `admin backup`, `config show`, `config set`, and `result copy` behind required foundations. `audit list` and `ingest list` remain deferred until canonical registries/contracts exist.

Sprint closure status: primary CRUD/progress targets are documented and tested; secondary P3 items remain intentionally deferred.

---

## 2. Concise Implemented / Deferred Command Summary

| Area | Implemented this sprint | Deferred |
|---|---|---|
| Run lifecycle | `run list`, `run show`, `run delete` | None from primary run scope |
| Plugin lifecycle | `plugin show`, `plugin delete` | None from primary plugin scope |
| Comparison Studio | `compare list`, `compare show`, `compare delete` | Legacy `compare create/export` remains decommissioned |
| Entity updates | `dataset update`, `algorithm update`, `masking update` | None from primary entity scope |
| Progress | `temporal prepare materialize`, `temporal experiment run`, `st experiment run`, `thesis all` | Remaining long operations outside sprint target |
| Secondary P3 | None implemented in this sprint | `admin status`, `lab list`, `lab stop`, `admin backup`, `config show`, `config set`, `result copy`, `audit list`, `ingest list` |

---

## 3. Commands Added

| Command | Patch | Status | Tests | Notes |
|---|---|---|---|---|
| `run list` | 06B-01 | Implemented | `tests/cli/test_run_crud.py` | Supports dataset, algorithm, status, limit, and table/JSON output. |
| `run show` | 06B-02 | Implemented | `tests/cli/test_run_crud.py` | Defaults to JSON, supports table output and `--include-results/--no-include-results`; summaries avoid embedding prediction arrays. |
| `run delete` | 06B-03 | Implemented | `tests/cli/test_run_crud.py` | Uses delete-impact inspection, dry-run, confirmation, record-only safe path, and explicit cascade. |
| `plugin show` | 06B-04 | Implemented | `tests/cli/test_plugin_crud.py` | Shows manifest, validation, contract/readiness, dependencies, tags, default config, and registered algorithm association. |
| `plugin delete` | 06B-05 | Implemented | `tests/cli/test_plugin_crud.py` | Quarantine-based deletion with dry-run, confirmation/force, cascade unregister, built-in refusal, and canonical-root containment. |
| `compare list` | 06B-06 | Implemented | `tests/cli/test_compare_crud.py` | Lists active, archived, or all Comparison Studio specs with table/JSON output. |
| `compare show` | 06B-06 | Implemented | `tests/cli/test_compare_crud.py` | Displays persisted Comparison Studio specs with JSON default and table option. |
| `compare delete` | 06B-06 | Implemented | `tests/cli/test_compare_crud.py` | Deletes only the `ComparisonSpec`; explicitly preserves results and artifacts. |
| `dataset update` | 06B-07 | Implemented | `tests/cli/test_entity_updates.py` | Patch-only semantics for name, path, description, format, and pollutants; empty update rejected. |
| `algorithm update` | 06B-07 | Implemented | `tests/cli/test_entity_updates.py` | Patch-only semantics for name, family, description, default config, dependencies; invalid family/JSON rejected. |
| `masking update` | 06B-07 | Implemented | `tests/cli/test_entity_updates.py` | Patch-only semantics for name, rate, params; invalid rate/JSON rejected. |

---

## 4. Commands Deferred

| Command | Reason | Classification |
|---|---|---|
| `admin status` | Safe read-only status is viable but was outside the primary sprint closure scope. | `READY` / P3-01 |
| `lab list` | Needs a durable `LabSession` model and session registry before listing active/stale sessions. | `REQUIRES_FOUNDATION` / P3-02 |
| `lab stop` | Safe stop needs managed session persistence and process identity validation before terminating any PID. | `REQUIRES_FOUNDATION` / P3-02 |
| `admin backup OUTPUT_DIR` | Needs `AdminBackupService`, source inventory, atomic manifest, checksums, and recursion protection. | `REQUIRES_FOUNDATION` / P3-03 |
| `config show` | Needs a typed canonical config service/schema and precedence model. | `REQUIRES_FOUNDATION` / P3-04 |
| `config set` | Needs typed mutable keys, validators, atomic writes, and rollback/backup behavior. | `REQUIRES_FOUNDATION` / P3-04 |
| `result copy` | Needs `ResultCloneService`, provenance, clone ID strategy, artifact policy, and rollback semantics. | `REQUIRES_FOUNDATION` / P3-05 |
| `audit list` | Audit wrappers lack a canonical audit registry with lifecycle/status metadata. | `DEFERRED` / P3-06 |
| `ingest list` | Product contract for ingestion history/current resources/failed attempts is not defined. | `DEFERRED` / P3-07 |

---

## 5. Progress Integrations Completed

| Command | Priority | Reporter Method | Preserved Artifacts |
|---|---:|---|---|
| `temporal experiment run` | P0 | Universal CLI `progress_options` + service-level `ProgressReporter` contexts/events | Temporal run identities, execution ledger/event logs, evidence outputs, JSON stdout discipline |
| `temporal prepare materialize` | P1 | `TemporalPreparationService.materialize(..., progress_reporter=...)` staged contexts | Mask-bank IDs, contract IDs, plan IDs, comparison-spec IDs, materialization report payloads |
| `st experiment run` | P2 | `STExperimentOrchestrator.run(..., progress_reporter=...)` stage contexts | Graph IDs, mask-bank IDs, plan/run IDs, rate/realization settings, gate/certification outputs, JSON stdout |
| `thesis all` | P3 | Thesis pack group/stage progress contexts and JSONL event logging | Ten pack outputs, `MANIFEST.json`, failure semantics that avoid false 100% completion |

Progress remains CLI-first and GUI-independent. Terminal progress and JSONL event logs are additive and do not alter scientific output identities.

---

## 6. Files Changed

Status is based on the working tree at sprint documentation closure.

| File | Change Type | Patch |
|---|---|---|
| `docs/CLI_Reference.md` | Modified | Final documentation update |
| `docs/CLI_Completeness_Sprint_Update_Report.md` | Created | Final documentation update |
| `docs/CLI_Completeness_Audit.md` | Created | Baseline audit input |
| `docs/CLI_Completeness_P3_Audit.md` | Created | P3 classification input |
| `imputebench/cli/runs_cmd.py` | Modified | 06B-01/02/03 |
| `imputebench/services/run_service.py` | Modified | 06B-03 |
| `imputebench/services/run_query_service.py` | Created | 06B-01/02 |
| `imputebench/cli/plugins_cmd.py` | Modified | 06B-04/05 |
| `imputebench/services/plugin_loader_service.py` | Modified | 06B-04 |
| `imputebench/services/plugin_lifecycle_service.py` | Created | 06B-05 |
| `imputebench/cli/compare_cmd.py` | Modified | 06B-06 |
| `imputebench/cli/datasets_cmd.py` | Modified | 06B-07 |
| `imputebench/cli/algorithms_cmd.py` | Modified | 06B-07 |
| `imputebench/cli/masking_cmd.py` | Modified | 06B-07 |
| `imputebench/cli/temporal_cmd.py` | Modified | 06A-01/02 |
| `imputebench/services/temporal/temporal_experiment_orchestrator.py` | Modified | 06A-01 |
| `imputebench/services/temporal/temporal_preparation_service.py` | Modified | 06A-02 |
| `imputebench/cli/st_cmd.py` | Modified | 06A-03 |
| `imputebench/services/spatiotemporal/st_experiment_orchestrator.py` | Modified | 06A-03 |
| `imputebench/cli/thesis_all_cmd.py` | Modified | 06A-04 |
| `tests/cli/test_run_crud.py` | Created | 06B-01/02/03 |
| `tests/cli/test_plugin_crud.py` | Created | 06B-04/05 |
| `tests/cli/test_compare_crud.py` | Created | 06B-06 |
| `tests/cli/test_entity_updates.py` | Created | 06B-07 |
| `tests/progress/test_temporal_progress_wiring.py` | Created | 06A-01/02 |
| `tests/progress/test_st_progress_wiring.py` | Created | 06A-03 |
| `tests/progress/test_thesis_progress_wiring.py` | Created | 06A-04 |
| `specs/SISYPHUS_IMPLEMENTATION_SHEET_06_CLI_COMPLETENESS_SPRINT.md` | Created | Sprint plan |
| `specs/SISYPHUS_IMPLEMENTATION_SHEET_05_UNIVERSAL_CLI_PROGRESS_REPORTING_FRAMEWORK.md` | Deleted/moved | Archive cleanup |
| `specs/archives/SISYPHUS_IMPLEMENTATION_SHEET_05_UNIVERSAL_CLI_PROGRESS_REPORTING_FRAMEWORK.md` | Created | Archive cleanup |

---

## 7. Destructive-Action Safety

| Command | Dry-Run | Confirmation | Cascade | Quarantine | Safe-Path |
|---|---|---|---|---|---|
| `run delete` | Yes: `--dry-run` reports impact only | Yes unless `--force` | Default `--no-cascade`; `--cascade` required for full deletion | No | Record-only deletion only when impact has no linked state; blocked otherwise |
| `plugin delete` | Yes: `--dry-run` reports impact only | Yes unless `--force` | Default cascade unregisters associated algorithm when safe | Yes: plugin bundle moved to `data/trash/plugins` or supplied quarantine dir | Refuses built-in plugins and paths outside configured plugin root |
| `compare delete` | Yes: `--dry-run` reports impact only | Yes unless `--force` | No result/artifact cascade by design | No | Deletes only `ComparisonSpec`; results and artifacts remain intact |
| `dataset update` | N/A (patch update) | N/A | N/A | N/A | Empty updates rejected; omitted fields preserved |
| `algorithm update` | N/A (patch update) | N/A | N/A | N/A | Empty updates rejected; invalid family/JSON rejected; omitted fields preserved |
| `masking update` | N/A (patch update) | N/A | N/A | N/A | Empty updates rejected; invalid rate/JSON rejected; omitted fields preserved |

---

## 8. Tests Run

### 8.1 Targeted Sprint Tests

Command run:

```bash
pytest -q tests/cli/test_run_crud.py tests/cli/test_plugin_crud.py tests/cli/test_compare_crud.py tests/cli/test_entity_updates.py tests/progress/test_temporal_progress_wiring.py tests/progress/test_st_progress_wiring.py tests/progress/test_thesis_progress_wiring.py
```

Result: **71 passed**.

Per-file breakdown, verified by running each file:

| Test file | Count | Result |
|---|---:|---|
| `tests/cli/test_run_crud.py` | 28 | passed |
| `tests/cli/test_plugin_crud.py` | 11 | passed |
| `tests/cli/test_compare_crud.py` | 10 | passed |
| `tests/cli/test_entity_updates.py` | 12 | passed |
| `tests/progress/test_temporal_progress_wiring.py` | 3 | passed |
| `tests/progress/test_st_progress_wiring.py` | 4 | passed |
| `tests/progress/test_thesis_progress_wiring.py` | 3 | passed |
| **Total** | **71** | **passed** |

### 8.2 Backward-Compatibility Checks

Required command checks run successfully:

```bash
python -m imputebench run create --help
python -m imputebench run execute --help
python -m imputebench run status --help
python -m imputebench plugin list
python -m imputebench dataset list
python -m imputebench algorithm list
python -m imputebench masking list
python -m imputebench compare --help
```

Result: **8/8 succeeded without error**.

---

## 9. Backward Compatibility

No existing checked command was removed or renamed. The following pre-sprint commands still resolve and execute/help successfully: `run create`, `run execute`, `run status`, `plugin list`, `dataset list`, `algorithm list`, `masking list`, and `compare --help`.

The sprint added commands beside existing surfaces instead of changing command names. The Comparison Studio `compare list/show/delete` command group is now the active comparison-spec lifecycle; legacy `compare create/export` had already been decommissioned in the baseline audit and was not removed by this documentation sprint.

---

## 10. Remaining Gaps

Remaining gaps are the P3-audited secondary items and non-primary long operations:

1. `admin status` can be implemented as a read-only P3 command with fast default checks and optional `--deep` scans.
2. `lab list` and `lab stop` require a managed `LabSession` registry and process identity checks.
3. `admin backup OUTPUT_DIR` requires atomic backup service design, manifest/checksum model, and source inventory.
4. `config show` and `config set` require a canonical typed config schema and controlled mutation semantics.
5. `result copy` requires clone semantics, provenance, artifact-copy policy, and rollback behavior.
6. `audit list` requires a canonical audit registry before exposing command inventory.
7. `ingest list` requires an ingestion-history/current-resource contract.
8. Progress wiring can be extended to remaining long-running operations after primary temporal/ST/thesis targets.

---

## 11. Closure Verdict

The CLI completeness sprint closed the primary command gaps identified in the baseline audit, completed priority progress integrations, documented the new CLI surface in `docs/CLI_Reference.md`, preserved destructive-action safety boundaries, and verified backward compatibility for the required existing commands.

*End of sprint closure report.*
