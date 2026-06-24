# Data Construction Contract — Legacy Migration

> Sheet 13 §16.3, §13-11. How the legacy LondonAQ dat-tsv route migrates to the
> canonical DCC pipeline with **exact** value parity.

## Actual current LondonAQ shape (do not substitute)

The audit baseline (`docs/audit/sheet13/BASELINE.md`) confirms the **actual**
current source — not the illustrative annual example:

| Property | Value |
|---|---|
| Source | `datasets/london_aq_synthetic/{NO,NO2,O3,SO2}.dat` |
| Per-file raw shape | `(6000, 14)` |
| Canonical legacy stack | **`(600, 10, 14, 4)`** = `(time, grid_row, grid_col, variable)` |
| Variables | NO, NO2, O3, SO2 (**4**, not 5) |
| dtype | float64, no missing, range `[1, 171]` |

The illustrative NetCDF example `(8760, 14, 10, 5)` with PM2.5/PM10/NO2/O3/SO2 is
a **different snapshot** and must never relabel the current files.

## Compatibility contract

`imputebench.services.data_contract.compatibility_contract` builds a
`legacy_dat_tsv` DCC from a dataset's *actual* persisted metadata
(`SpatialInfo`/`TemporalInfo`/pollutants):

```python
from imputebench.services.data_contract.compatibility_contract import (
    build_legacy_contract_for_dataset,
)
contract = build_legacy_contract_for_dataset(dataset)
```

All shape facts (`flattened_time_rows = time * grid_row`, `grid_cols_in_file`,
the `(time, grid_row, grid_col)` reshape) live in the contract and the
`LegacyDatTsvAdapter` — **never** as literals in `DatasetService`.

## Exact parity

`tests/data_contract/test_legacy_london_parity.py` proves:

```python
np.testing.assert_allclose(legacy_output, dcc_output, equal_nan=True)
```

where `legacy_output` is the pre-Sheet-13 loader (`reshape(600, 10, 14)` then
`np.stack(..., axis=-1)`) and `dcc_output` is the adapter + canonicalizer output.
The canonical output is C-contiguous float64.

## Literal-removal gate

After parity, `DatasetService` contains **zero** legacy shape literals
(`(6000, 14)`, `reshape(600, ...)`, `timestep_count=600`, `spatial_shape=(10,14)`).
The legacy read path routes through `LegacyDatTsvAdapter.read_variable` +
the auto-generated compatibility contract. A regression test enforces the gate.

## Migration steps (§16.3)

1. Add the DCC tables + optional `Dataset` fields (schema v11). Retain `dataset_files`.
2. Auto-generate a compatibility contract per accessible `dat-tsv` dataset.
3. Validate the actual source shape; materialize the canonical artifact.
4. Mark the dataset `ready` only after success; retain source path + adapter id.
5. Inaccessible legacy data stays `legacy_unvalidated`.

`DatasetService.load_array` prefers the validated canonical `data.npy` when
present (`validation_status == "ready"` and a `canonical_artifact_path`), and
falls back to the literal-free legacy adapter path otherwise.
