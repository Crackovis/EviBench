# Data Construction Contract (DCC)

> Sheet 13 — contract-driven environmental tensor ingestion.

The Data Construction Contract is a versioned, strict, human-editable document
that describes how an external data resource becomes the **canonical EviBench
tensor** in `(time, grid_row, grid_col, variable)` order. A new conforming
dataset can be registered with **no Python changes** — only a contract.

```
external resource + DCC
  -> schema validation
  -> format adapter inspection
  -> axis mapping
  -> canonicalization
  -> strict semantic validation
  -> atomic registration
  -> canonical tensor artifact
  -> dataset selector
  -> Lane 1 station view or Lane 2 full-grid view
```

## Three separated concepts

1. **Source representation** — NetCDF, NPY, NPZ, or the legacy pollutant-file directory.
2. **Canonical dataset snapshot** — a validated rank-4 tensor `(time, grid_row, grid_col, variable)`.
3. **Experiment view** — the complete tensor for Lane 2, or a rank-preserving
   station view `(time, 1, 1, variable)` for Lane 1.

## Authoring a contract

The preferred form is `*.dcc.yaml` (UTF-8 YAML 1.2) — JSON is equally accepted.
A contract begins with:

```yaml
contract:
  kind: imputebench.data-construction-contract
  schema_version: 1.0.0
```

Required root blocks: `contract, dataset, source, tensor, dimensions, variables,
missing_data, station_selection, provenance, validation`. Optional:
`lane_defaults, extensions`. **Unknown normative fields are rejected** — put
vendor values under `extensions`.

The JSON Schema (draft 2020-12) ships at
`imputebench/resources/schemas/data_construction_contract_v1.schema.json` and is
printed by `imputebench data contract schema`.

See full worked examples:

- `docs/examples/data_contracts/london_aq_synthetic.dcc.yaml` — illustrative
  annual NetCDF snapshot `(8760, 14, 10, 5)`.
- `docs/examples/data_contracts/legacy_londonaq_compat.dcc.yaml` — the **actual**
  current legacy dat-tsv snapshot `(600, 10, 14, 4)` (NO, NO2, O3, SO2).

> **YAML gotcha:** pollutant labels such as `NO` are YAML 1.1 booleans. Quote
> them (`"NO"`, `id: "no"`) in `variables` and coordinate `labels`.

## Canonical artifact layout

For `import_mode: copy` a registered dataset materializes:

```
data/datasets/<dataset_id>/<dataset_version>/
├── source/        (copied resource + authored_contract.dcc.yaml)
├── canonical/     (data.npy, observed_mask.npy, station_index.json)
├── metadata/      (normalized_contract.json, source_inspection.json,
│                   validation_report.json, fingerprints.json)
└── COMPLETE       (written last; runtime rejects a directory without it)
```

`canonical/data.npy` is rank-4, `(T, R, C, V)` order, float32/float64,
C-contiguous, declared missing values as `NaN`, free of infinity, immutable.
`observed_mask.npy` is `np.isfinite(data)`.

## Strict validation

Registration runs ordered phases V1–V10 (syntax → JSON Schema → source access →
header → axis mapping → full canonical scan → station index → provenance/
fingerprints → staging reopen → atomic registration). Rejections carry a
machine-readable `DCC-*` code (see Sheet 13 §11). No dataset becomes visible
before full-scan validation and the artifact reopen check pass.

## Fingerprints and identity

```
dataset_snapshot_fingerprint = sha256(normalized_contract_sha256
                                       + canonical_tensor_sha256
                                       + station_index_sha256)
station_view_fingerprint     = sha256(dataset_snapshot_fingerprint
                                       + selector_kind + row + col + station_id)
```

Dataset identity is `slug + version + canonical data fingerprint`. Two different
fingerprints MUST NOT share one immutable version (re-registration with a
different fingerprint is rejected with `DCC-REGISTER-001` unless `--overwrite`).

## Lanes and views

- **Lane 2** consumes the complete rank-4 tensor.
- **Lane 1** consumes a rank-preserving station view obtained with
  `array[:, row:row+1, col:col+1, :]` → `(T, 1, 1, V)` (never scalar indexing).
  The time-major boundary flattens it to `(T, V)`.

There is **no silent `(0,0)` default station**. A `station_required` Lane 1
dataset requires either `--station-row/--station-col` or `--station-id`.

Two station views are different supports. Their view fingerprints flow through
the temporal task, execution identity, run, result, and mask-bank identity, so a
task completed on one station never satisfies `--skip-completed` for another.

## Programmatic API

- `DataConstructionContractService` — `parse / inspect / validate / get / get_for_dataset`.
- `ContractDrivenDatasetIngestionService` — `dry_run / register`.
- `DatasetService` — `get_contract / load_array / validation_report / list_stations`.
- `DatasetViewService` — `full_grid / select_station / resolve_selector / list_stations`.
- `DatasetRuntimeProvider.load(dataset_id, *, view=DatasetViewSpec | None)`.

See `docs/Data_Construction_Contract_CLI.md` for the command surface and
`docs/Data_Construction_Contract_Migration.md` for the legacy migration.
