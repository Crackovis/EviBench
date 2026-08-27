# Result Evidence Summary — CubicSpline / protocol:test_r000

> **Scientific status:** [SCIENTIFIC]  
> **Target:** `183a4efd-d24a-4ad0-9062-adf68bb86e04`  

## Identity

| Field | Value |
|---|---|
| Run | official_wuhan_interpolation_comparison:mar:10:CubicSpline:official (`c190ccfb-08c1-4def-8885-823f890f17a4`) |
| Dataset | Wuhan Air Quality (Real) (`c1b03ed1-dad4-444b-a06d-e85cb81378b6`) |
| Algorithm | CubicSpline — classical (`cubic_spline`) |
| Masking | protocol:test_r000 — mar, declared rate 0.1 |
| Realized rate | n/a |
| Phase | execute |
| Realization | n/a |
| Execution class | requested: classical; actual: classical |
| Domain | temporal_imputation |
| Status | COMPLETED |
| Created | 2026-08-17T08:25:17.906477+00:00 |
| Completed | 2026-08-17T08:25:17.906445+00:00 |

## Global Metrics

| Metric | Value | Scope | Source |
|---|---|---|---|
| mae | 2.7476 | global | `Result.metrics` |
| rmse | 5.4190 | global | `Result.metrics` |
| runtime_s | 3.1296 | global | `Result.metrics` |

## Per-Pollutant Metrics

| Pollutant | MAE | RMSE |
|---|---|---|
| co | 0.0795 | 0.1596 |
| no2 | 3.9266 | 6.7118 |
| o3 | 5.2693 | 8.3053 |
| pm10 | 3.1598 | 5.5658 |
| pm25 | 3.1869 | 5.0736 |
| so2 | 0.8634 | 2.3283 |

## Node Metric Summary

| Field | Value |
|---|---|
| Node count | 10 |
| Metric | mae |
| Coverage | 10 |
| Min | 2.0057 |
| Median | 2.7321 |
| Mean | 2.7170 |
| Max | 3.2272 |
| Worst node | node_6 |

## Runtime Breakdown

| Stage | Duration | Source |
|---|---|---|
| Total | 3277.0 ms | native_timing_spans |
| Fit | 84.5 ms | native_timing_spans |
| Inference | 3131.8 ms | native_timing_spans |
| Metrics | 57.2 ms | native_timing_spans |
| Persistence | 0.0 ms | native_timing_spans |

**Runtime evidence quality:** native_timing_spans  
**Coverage:** 5/5 stages  
**Timing spans:** 5

## Benchmark and Comparability

| Field | Value |
|---|---|
| Benchmark contract | f55492f8-f0db-4e31-9861-3e2cc7637eb3 |
| Mask bank | 20c9ee48-338f-4c95-913e-9dede901f7b6 |
| Benchmark realization | test_r000 |
| Evaluation-support fingerprint | c20ecea7d5d435f8addbc5bb3795c002671eef03e467fabff9fc33b059b0ddba |
| Comparison posture | benchmark_ready |

## Artifact Readiness

| Role | State | Portable path |
|---|---|---|
| Predictions | declared | repo://data/results/c190ccfb-08c1-4def-8885-823f890f17a4/183a4efd-d24a-4ad0-9062-adf68bb86e04/X_imputed.npy |
| Mask | declared | repo://data/benchmark_mask_banks/official_wuhan_interpolation/mar/10/test/test_r000 |
| Checkpoint | missing | missing |
| Training history | missing | missing |

## Provenance

| Field | Value |
|---|---|
| Recipe book | official_wuhan_interpolation_comparison |
| Recipe revision | n/a |
| Recipe profile | a |
| Recipe entry | official_wuhan_interpolation_comparison:mar:10 |
| Materialization | n/a |
| Git commit | c5676286ea8a4eff3bd558a55650578c63fe7c00 |
| ImputeBench version | 1.2.0 |
| Python version | 3.13.9 |
| Environment digest | b2d0eaccf552b37655f5fcc3de42e391590f79c7d7b4dedf9fc8e0b2f5826735 |

## Caveats

- None.

## Claim Boundary

- Scientific. Metrics computed under a shared benchmark contract and may be ranked and compared.
