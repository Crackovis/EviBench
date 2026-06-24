# Sheet 13 — Audit Freeze Baseline

**Captured at:** start of Sheet 13 implementation on branch `claude/relaxed-goodall-w754qw`.
**Purpose:** Record the *actual* current LondonAQ state so the migration generates a
compatibility contract from the real persisted source shape — never from the
illustrative `(8760, 14, 10, 5)` annual example used elsewhere in the sheet.

---

## 1. Actual current LondonAQ shape (CONFIRMED, do not substitute)

The active dataset lives at `datasets/london_aq_synthetic/` and is seeded by
`scripts/seed_london_dataset.py` with pollutant order **`["NO", "NO2", "O3", "SO2"]`**.

| Property | Confirmed value |
|---|---|
| Source format | `dat-tsv` (one `<pollutant>.dat` per variable, tab-separated) |
| Per-file raw shape | `(6000, 14)` |
| Per-file reshape | `(600, 10, 14)` |
| Variables (V) | **4** — `NO, NO2, O3, SO2` (NOT 5; the illustrative example uses PM2.5/PM10/NO2/O3/SO2) |
| Canonical legacy stack | **`(600, 10, 14, 4)`** = `(time, grid_row, grid_col, variable)` |
| dtype | `float64` |
| C-contiguous | **No** (result of `np.stack(..., axis=-1)`; canonicalization MUST force C-order) |
| Missing values | **0** NaNs in raw data |
| Observed value range | `[1.0, 171.0]` |

> The illustrative London AQ Synthetic NetCDF example in the sheet
> (`(8760, 14, 10, 5)`, 5 pollutants, hourly annual) is a **different snapshot**.
> The compatibility contract for the *current* files MUST declare
> `shape: [600, 10, 14, 4]` and variables `[NO, NO2, O3, SO2]`.

### Grid orientation (from `DATASET_CHARACTERIZATION.md`)
- 14 columns (horizontal, labeled A–N) × 10 rows (vertical, rows 1–10) per timestep.
- `6000` file rows = `10` grid rows × `600` timestamps; every 10 consecutive rows are one timestep.
- Loader `reshape(600, 10, 14)` ⇒ axis 1 (size 10) = `grid_row`, axis 2 (size 14) = `grid_col`.
- `SpatialInfo(rows=10, cols=14, n_nodes=140)` in the `Dataset` default matches this.

---

## 2. Loader assumptions (legacy, to be moved behind a contract)

`imputebench/services/dataset_service.py` hard-codes the legacy layout:

| Line | Literal | Meaning |
|---|---|---|
| 174 | `"timestep_count": 600` | `inspect_dataset_header` returns fixed 600 |
| 175 | `"spatial_shape": (10, 14)` | fixed grid in `inspect_dataset_header` |
| 269 | `if matrix.shape != (6000, 14)` | per-file shape gate in `_load_pollutant` |
| 271 | `return matrix.reshape(600, 10, 14)` | fixed reshape in `_load_pollutant` |

`load_frame()`, `load_window()`, `load_array()`, `missing_files()`, and
`inspect_dataset_header()` all assume `<dataset path>/<pollutant>.dat`.

**Gate for patch 13-05:** after canonical loading lands, the count of
`(6000,14)`, `(600,10,14)`, `timestep_count=600`, `spatial_shape=(10,14)`
literals remaining in `DatasetService` must be **0**.

---

## 3. Resource ingestion behavior (legacy)

`imputebench/services/resource_ingestor_service.py`:
- Scans only `.csv` / `.dat`; recognizes a fixed vocabulary
  `POLLUTANT_KEYS = ("PM2.5", "PM10", "NO2", "SO2", "NO", "O3")`.
- Requires ≥ 2 pollutant files; registers every dataset as `format="dat-tsv"`.
- Performs **no** full-tensor validation before persistence.

---

## 4. CLI surface (legacy)

```
imputebench data
├── dataset   (list | register | update | show | delete)
├── masking
├── ingest    (inspect | dataset | plugin | bundle)
└── calibration
```

`dataset register` accepts only `--name --path --format --pollutants`.
No contract validation or canonicalization occurs.

---

## 5. Runtime boundary (already correct, preserve)

```
execution services -> DatasetRuntimeProvider -> DatasetService -> canonical storage
```

`DatasetRuntimeProvider.load()` calls `DatasetService.load_array()` and builds a
frozen `DatasetRuntimeContext`. Experiment runners never call source-format
adapters. Sheet 13 extends — not bypasses — this boundary.

`flatten_time_major()` (in `imputebench/algorithms/utils.py`) preserves axis 0 and
flattens the rest: `(T,1,1,V) -> (T, V)`. No temporal algorithm needs station
coordinates.

---

## 6. Temporal identity (for Lane 1 station propagation)

- `TemporalExperimentTask` (`imputebench/domain/temporal/temporal_experiment.py`) is frozen.
- Dedup is driven by `TemporalExecutionIdentity.identity_key` — a pipe-delimited
  string (`imputebench/domain/temporal/temporal_execution_identity.py`). A station/
  view fingerprint folded into this key makes `--skip-completed` station-aware so a
  task completed on one station never satisfies skip for another.
- `ExperimentRun` / `Result` carry benchmark + recipe-lineage identity fields that
  the station/view identity must travel alongside.

---

## 7. Mask-bank identity (for Lane 1 station mask banks)

`benchmark_mask_bank_service.py`:
- `_derive_split_fingerprint(dataset_id, phase, data_shape)` — sha256 of those fields.
- `_derive_eligibility_fingerprint(eligible_mask)` — sha256 of mask bytes.

A station-view fingerprint must enter mask-bank identity so a full-grid realization
is never silently applied to a `(T,1,1,V)` station view.

---

## 8. Test/environment baseline

- Project pins `numpy>=1.24,<2.0`. Working scientific stack for this session:
  numpy 1.26.4, pandas 2.2.3, scipy 1.11.4, scikit-learn 1.3.2, xarray, h5netcdf.
- Many existing test modules require heavy optional deps (streamlit, torch) and do
  not collect in this environment; DCC tests are written to run independently.
- Green baseline confirmed before changes:
  `tests/test_b5_5_dataset_frame_loading.py`,
  `tests/test_p5_dataset_file_repository.py`,
  `tests/test_j0_storage_contract_json_matrix.py` (23 passed).
