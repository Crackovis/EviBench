# MOT03 Mask Bank Archive Blindness Implementation Report

Implementation of `MASK_BANK_ARCHIVE_BLINDNESS_EviBench_ROBUST.md`.
Local implementation only: no push, no duplicate deletion, no schema migration.

## Root Cause

`benchmark_mask_banks.archived` and `payload_json.archived` can diverge when an
archive fix is applied directly in SQL. The repository previously returned only
`payload_json`, so a SQL-archived mask bank could still be reconstructed as
active and reach the official recipe resolver.

The same normalized-column plus JSON pattern exists for benchmark contracts, so
the same overlay was applied there.

## Files Changed

| File | Change |
|---|---|
| `imputebench/persistence/repositories/benchmark_repository.py` | `get()` and `list_all()` now select `payload_json, archived` and overlay SQL `archived` onto decoded payloads for mask banks and contracts. SQL filtering for `include_archived=False` is unchanged. |
| `imputebench/services/database_hygiene_service.py` | Added explicit archive-consistency audit for benchmark mask banks and contracts. |
| `imputebench/cli/admin/db_validate.py` | Added `--check benchmark-archive-consistency`. |
| `tests/persistence/test_benchmark_repository_archived_overlay.py` | Repository overlay tests for mask banks and contracts, plus hygiene diagnostic coverage. |
| `tests/comparison/test_mask_bank_archive_blindness.py` | Service transport and resolver regression tests for SQL-archived duplicates and real ambiguity. |
| `docs/audit/mot03_mask_bank_archive_blindness/` | Baseline, prepare validation summary, and this report. |

## Behavioral Guarantees

- `BenchmarkMaskBankRepository.get()` returns `archived` from the SQL column.
- `BenchmarkMaskBankRepository.list_all(include_archived=True)` includes archived
  rows but returns the SQL-authoritative archive flag.
- `BenchmarkMaskBankRepository.list_all(include_archived=False)` still filters in SQL.
- `BenchmarkContractRepository` has the same overlay behavior.
- `BenchmarkMaskBankService.list()` can still list archived banks, but the returned
  model carries the correct `archived` value.
- `OfficialRecipeAssetIdentityResolver` still ignores archived candidates and still
  reports `ambiguous` when two non-archived exact candidates remain.

## Before And After Counts

Baseline database inspection found:

| Metric | Before overlay |
|---|---:|
| `benchmark_mask_banks` total | 94 |
| Mask-bank SQL/payload archive mismatches | 29 |
| `benchmark_contracts` total | 91 |
| Contract SQL/payload archive mismatches | 0 |
| MCAR 0.3 total candidates | 12 |
| MCAR 0.3 active by SQL column | 1 |
| MCAR 0.3 active by payload | 12 |
| MCAR 0.3 SQL/payload mismatches | 11 |

After overlay, repository consumers see the SQL column as authoritative. MCAR 0.3
therefore has one active exact mask-bank candidate for the official LondonAQ
classical recipe instead of 12 payload-active candidates.

## Prepare Validation After

The spec's `prepare status` command is not present in this CLI build. The
equivalent non-mutating check used was:

```bash
python -m imputebench experiment temporal prepare dry-run --recipe-book official_londonaq_classical_benchmark --tier a --dataset-id london_aq --output-dir docs/exports/temporal
```

Result summary:

| Field | Value |
|---|---|
| Status | `ready` |
| Existing mask banks | 6 |
| Existing contracts | 6 |
| Errors | `[]` |
| MCAR 0.3 mask bank status | `exact` |
| MCAR 0.3 mask bank id | `cabf2377-f3e9-4b41-b24d-1d78a0f782b7` |
| MCAR 0.3 contract status | `exact` |
| MCAR 0.3 contract id | `4ccbfbe9-ab33-4ce6-a54c-063cf13c4009` |

The detailed summary is in `PREPARE_STATUS_AFTER.json`.

## Diagnostic

The new diagnostic reports SQL/payload archive drift without failing the default
audit path:

```bash
python -m imputebench admin db-validate --check benchmark-archive-consistency --format json --limit 3
```

Observed on the local database: 29 warning findings, 0 blockers, 29 repairable.
This is expected because the repository now corrects behavior at read time while
the payloads remain stale until a future repair command resynchronizes them.

## Tests Run

| Command | Result |
|---|---|
| `pytest tests\persistence\test_benchmark_repository_archived_overlay.py tests\comparison\test_mask_bank_archive_blindness.py -q` | 10 passed |
| `pytest tests\test_phase19_official_recipe_asset_identity_resolver.py -q` | 3 passed |
| `pytest tests\test_s8_benchmark_services_sqlite_strict.py -q` | 2 passed |
| `pytest tests\test_phase21_artifact_catalog_plan_gate.py -q` | 6 passed |
| `pytest tests\persistence\test_recipe_book_repository.py -q` | 19 passed, 34 existing deprecation warnings |
| `pytest tests\admin\test_admin_status_service.py tests\admin\test_admin_backup_service.py tests\test_jm2_admin_data_management.py -q` | 27 passed |

## Remaining Ambiguities

No MCAR 0.3 ambiguity remains for the official LondonAQ classical prepare dry-run.
If future data contains multiple non-archived exact mask banks, the resolver will
continue to return `ambiguous` by design.
