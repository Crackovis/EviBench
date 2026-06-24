# Data Construction Contract — CLI Reference

> Sheet 13 §15. All commands live under `imputebench data`.

```
imputebench data
├── contract
│   ├── schema       print the versioned JSON Schema
│   ├── inspect      schema + adapter + transpose plan (no mutation)
│   ├── validate     full-scan strict validation (no mutation)
│   └── show         show a registered dataset's contract
├── ingest
│   └── dataset      register (DCC mode) or legacy resource ingest
└── dataset
    ├── list | show | register | update | delete   (existing)
    ├── stations     list the persisted station index
    └── slice        extract a rank-preserving station view (T,1,1,V)
```

## contract

```bash
imputebench data contract schema --version 1.0.0 --format json

imputebench data contract inspect CONTRACT [--source PATH] [--format table|json]

imputebench data contract validate CONTRACT \
  [--source PATH] [--full-scan/--header-only] [--report PATH] [--format table|json]
# --header-only prints "NOT ELIGIBLE FOR REGISTRATION"

imputebench data contract show DATASET_ID [--normalized/--authored] [--format yaml|json]
```

## ingest (DCC registration)

```bash
imputebench data ingest dataset \
  --contract london_aq_synthetic.dcc.yaml \
  --source london_aq_synthetic.nc \
  --import-mode copy \
  [--dataset-id TEXT] [--dry-run] [--overwrite] \
  [--keep-failed-staging] [--report PATH] [--format table|json]
```

- `--dry-run` validates and writes nothing.
- An invalid contract/data exits non-zero with `DCC-*` codes; no partial row or
  `COMPLETE` marker is written.
- Legacy mode is preserved: `imputebench data ingest dataset PATH --name NAME`.

## dataset stations / slice

```bash
imputebench data dataset stations DATASET_ID [--active-only/--all] [--format table|json]

imputebench data dataset slice DATASET_ID \
  (--row INT --col INT | --station-id TEXT) \
  [--output PATH] [--format npy|npz] [--metadata PATH]
```

## Temporal Lane 1 station selection (Sheet 13 §13.5)

`imputebench experiment temporal experiment` commands accept:

```
--dataset-view [station|full-grid]
--station-row INTEGER
--station-col INTEGER
--station-id TEXT
```

Rules: row and col must be supplied together; `--station-id` is mutually
exclusive with row/col; `--dataset-view station` requires a selector; supplying
a selector implies a station view.

```bash
imputebench experiment temporal experiment run \
  --recipe-book official_londonaq_classical_benchmark \
  --tier a \
  --dataset-id london_aq_synthetic \
  --dataset-view station --station-row 3 --station-col 7 \
  --algorithms LinearInterpolation,NearestInterpolation
```

A station completed under `--skip-completed` never satisfies another station's
task: the station identity is folded into the temporal execution identity key.

## NetCDF dependencies

The NetCDF adapter needs the optional `data-io` extra. When missing the CLI
emits:

```
DCC-IO-004 NetCDF adapter dependencies are unavailable.
Install: pip install -e ".[data-io]"
```
