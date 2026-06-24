# Result Evidence Summary Format (Sheet 11B §6)

`result_summary` (provider `core_result`) is the human- and machine-readable
summary of a result or run.

## Files

| File | When |
|---|---|
| `result_summary.md` / `.json` | always (result target) |
| `global_metrics.csv` | always |
| `pollutant_metrics.csv` | when per-pollutant metrics exist |
| `runtime_breakdown.csv` | always |
| `run_summary.md` / `.json`, `result_index.csv` | run target |

CSV sidecars are provider-required and are written even when the requested
primary formats are Markdown/JSON.

## JSON schema (`imputebench.result-summary/v1`)

```json
{
  "schema": "imputebench.result-summary/v1",
  "target": {"type": "result", "id": "..."},
  "identity": {},
  "metrics": {"global": {}, "split": {}, "pollutant": {}, "node_summary": {}},
  "runtime": {},
  "benchmark": {},
  "artifacts": [],
  "provenance": {},
  "scientific_status": {},
  "caveats": [],
  "claim_limits": []
}
```

## Rules

- **Never mix metric scopes**: global = `Result.metrics`, split =
  `Result.split_metrics`, pollutant = `Result.pollutant_metrics`. Node metrics
  are summarised (count, coverage, min/median/mean/max, worst node) — the full
  node table belongs to `node_metric_summary`.
- **Runtime source quality** is explicit and never collapses a native timing
  span and a legacy `runtime_s` into the same grade. Resolution order: native
  spans → `runtime_summary_payload` → legacy `metrics.runtime_s` → missing.
- **Scientific status badges** (text, stable): `[SCIENTIFIC]`,
  `[REPORTABLE WITH CAVEATS]`, `[EXPLORATORY ONLY]`, `[DIAGNOSTIC]`,
  `[MOCK / NON-SCIENTIFIC]`, `[FAILED]`, `[INCOMPLETE]`.
- **Benchmark posture**: missing contract → `exploratory_only` with the
  canonical `EXPLORATORY ONLY` warning; a contract is never inferred.
- **Canonical paths are portable** (`repo://…`); JSON is `allow_nan=False`
  (NaN/Inf are serialised as `null`).
- A failed/pending/mock result still exports identity, status, and provenance;
  the metrics section reports unavailability without successful-result language.
