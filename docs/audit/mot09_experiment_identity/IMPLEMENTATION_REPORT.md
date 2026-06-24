# MOT 09 — Implementation Report

End-to-end implementation of *Experiment Identity and Data Isolation for
EviBench-VIP* per `specs/EXPERIMENT_IDENTITY_AND_DATA_ISOLATION_EviBench_ROBUST.md`.

## Definition of done (§20)

`exp1` and `exp2` can share dataset, recipe book, algorithms and DB **without**
cross-contaminating execution, export, source summaries, site packs or cleanup.
The first-order `experiment_id` data scope is propagated end-to-end:

```
experiment registry
→ TemporalExperimentRunRequest → TemporalExperimentTask → ExperimentRun → Result
→ ArtifactRecord → RuntimeTimingSpan → source result_summary → human export selection
→ site experiment state → cleanup
```

## Patches delivered

| Patch | Scope | Key files |
|---|---|---|
| MOT09-01 | Schema v13 + migration | `storage/sqlite/schema.py`, `storage/sqlite/migrations.py` (`_migrate_to_13`) |
| MOT09-02 | Domain models + mappers | `models/{experiment_run,result}.py`, `domain/temporal/temporal_experiment.py`, `persistence/mappers/*`, `persistence/repositories/*`, `read_models/runtime_timing.py`, `services/runtime_timing_service.py`, `services/result_service.py` |
| MOT09-03 | Registry service | `services/experiment/experiment_registry.py`, `persistence/repositories/experiment_repository.py` |
| MOT09-04 | Temporal CLI + orchestrator | `cli/experiment/temporal/shared.py`, `services/temporal/temporal_experiment_orchestrator.py`, `services/temporal/temporal_execution_dispatcher.py`, `services/execution/runner/classical_execution.py` |
| MOT09-05 | Selection SQL filter | `read_models/results_interaction/selection.py`, `persistence/queries/result_selection_queries.py`, `services/results_interaction/selection_service.py` |
| MOT09-06 | Human export integration | `domain/evidence/human_export.py`, `services/.../human_export/{selection_service,human_evidence_export_service,site_experiment_state_service}.py`, `cli/results/export_human.py` |
| MOT09-07 | Source summaries/provenance | `services/results_interaction/providers/_projections.py`, `services/.../human_export/source_reader.py` |
| MOT09-08 | Cleanup/archive | `services/experiment/experiment_cleanup_service.py` |
| MOT09-09 | Legacy inventory/backfill | `services/experiment/experiment_backfill_service.py`, `cli/experiment/registry.py` |

## Schema (§4, §17.1)

`SCHEMA_VERSION = 13`. Migration `_migrate_to_13` is additive and non-destructive:

* `experiments` registry table + `idx_experiments_status`.
* nullable `experiment_id` on `runs`, `results`, `artifact_records`,
  `runtime_timing_spans`, with the seven experiment indexes.
* backfill **only** from `payload_json.experiment_id` (and timing `attrs_json`);
  never invented, never assigned by algorithm heuristic. Legacy rows stay NULL.
* Verified on a copy of the production DB (2063 results / 506 runs / 1162
  artifacts / 11646 timing spans): all rows remain unscoped (NULL), idempotent.

## Design decisions / hardening

* **`experiment_domain` ≠ `experiment_id`** (§2.1). A new field was added; the
  workflow-family column was left untouched.
* **Phase-1 enforcement** (§2.2): only an explicit `--official` run without a
  scope and without `--legacy-unscoped` is *blocked*; other unscoped runs only
  warn. This avoids breaking smoke/dev runs and the existing official-binding
  tests (which set the official recipe book but not the `official` flag).
* **No heuristic backfill** (§2.3, §13.1): `suggested_experiment_id` is always
  empty (`confidence=none`); only rows with a user-filled
  `user_approved_experiment_id` are written, transactionally, with an audit.
* **Mask banks stay global** (§2.4): cleanup classifies mask-bank / dataset /
  recipe-book / contract artifacts as *shared* and never deletes them.
* **Cleanup is dry-run first** (§2.5): `purge` requires `--apply
  --i-understand-data-loss`, takes a pre-purge SQLite backup, deletes only the
  experiment's exclusive purgeable rows/files, and leaves other experiments and
  shared paths intact.
* **Backward-compatible selection** (§9.1): the SQL legacy-exclusion is gated on
  an explicit `exclude_unscoped` flag that the human-export layer sets by default
  for recipe/tier exports; the generic selection service default is unchanged, so
  the 711 existing human_evidence/results_interaction tests are unaffected.
* **`publish_as` decouples** the site directory from the data scope (§10.2); the
  site state records `data_experiment_id`, `publish_as`, `selection_scope`,
  `legacy_unscoped`. A merge can never cross experiment scopes (§10.5).
* **Scope-mismatch guard** (§9.2): explicit `result_ids` whose `experiment_id`
  positively differs from `--experiment-id` raise `HRE-EXP-SCOPE-MISMATCH`;
  untagged legacy ids are governed by the legacy modes, not flagged.

## CLI added

```
imputebench experiment registry create|list|show|archive|refresh-counts EXPERIMENT_ID
imputebench experiment registry inventory-legacy [--output ...]
imputebench experiment registry plan-backfill --output plan.csv
imputebench experiment registry apply-backfill --plan plan.csv --apply [--auto-create]
imputebench experiment registry quarantine-legacy [EXPERIMENT_ID] [--apply]
imputebench experiment registry cleanup-plan EXPERIMENT_ID [--output ...]
imputebench experiment registry purge EXPERIMENT_ID --apply --i-understand-data-loss
imputebench experiment temporal experiment run --experiment-id ... [--legacy-unscoped] [--auto-create-experiment]
imputebench results export-human --experiment-id ... [--publish-as ...] [--legacy-unscoped]
```

## Tests (§14)

`tests/experiment_identity/` — 81 passed:

```
test_schema_migration_experiment_id.py          test_result_selection_experiment_filter.py
test_run_result_experiment_propagation.py        test_legacy_unscoped_mode.py
test_experiment_registry_service.py              test_explicit_result_scope_mismatch.py
test_experiment_id_validation.py                 test_export_human_experiment_scope.py
test_temporal_cli_experiment_id.py               test_site_publish_as.py
test_experiment_merge_scope.py                   test_source_summary_experiment.py
test_cleanup_plan.py                             test_purge_safety.py
test_backfill_plan_apply.py                      test_registry_cli.py
```

### Regression

| Suite | Result |
|---|---|
| `tests/experiment_identity` | 81 passed |
| `tests/human_evidence` + `tests/results_interaction` + `tests/temporal` | 819 passed |
| timing / ST persistence / sheet17 / targeted-migration spot checks | 24 passed |

> Note: the full `pytest -q` has ~600 pre-existing, unrelated collection/import
> failures (missing `algorithm_execution_truth_service` etc.) tracked separately;
> focused suites are the canonical gate per project convention.

## Acceptance criteria (§17) — status

* Schema: `experiments` table + four `experiment_id` columns + indexes; legacy
  rows NULL — **yes**.
* Execution: official run without scope blocked unless legacy; scope propagates
  request→task→run→result; artifacts/timing tagged; registry status updates — **yes**.
* Selection/export: `--experiment-id` filters SQL; recipe/tier excludes unscoped
  legacy by default; `--legacy-unscoped` explicit; explicit mismatch blocked;
  `--publish-as` works; A+B merge cannot cross experiments — **yes**.
* Migration/backfill: no algorithm heuristic; inventory + plan + approved-only
  transactional apply + audit — **yes**.
* Cleanup: archive soft; cleanup-plan dry-run; purge double-confirmed; shared
  mask banks kept; other experiments untouched — **yes**.

## Deferred (§19)

Cross-experiment aggregate command, remote registry, mask-bank usage table, and
UI experiment browser remain out of scope.
