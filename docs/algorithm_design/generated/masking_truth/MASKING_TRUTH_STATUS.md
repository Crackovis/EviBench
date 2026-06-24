# Experiment Lifecycle Masking Truth Audit (LC1)

**Generated:** 2026-05-08 (aggregated — UUID detail redacted for public documentation)
**Filters:** run_id=—, result_id=—, family=—, dataset=—, official_only=True, phase=test

## 1. Summary counts

| Metric | Count |
|---|---|
| Total results audited | 837 |
| Thesis-ready | 405 |
| Degraded | 0 |
| Diagnostic-only | 360 |
| Blocked | 0 |
| Audit errors | 0 |

## 2. Dataset natural missingness status

| Status | Count |
|---|---|
| Natural missingness detected (>0 cells) | 0 |
| No natural missingness (controlled benchmark) | 837 |

## 3. Classical lifecycle masking truth (aggregate)

All 9 classical/baseline algorithms were evaluated across missingness scenarios (MCAR, MAR, MNAR at 0.1, 0.3, 0.5 rates).

| Algorithm | Family | Phase | Results | Mask Status | Thesis Status |
|---|---|---|---|---|---|
| Mean | naive | execute | 45 | exact | thesis_ready |
| Median | naive | execute | 45 | exact | thesis_ready |
| LinearInterpolation | naive | execute | 45 | exact | thesis_ready |
| LOCF | naive | execute | 45 | exact | thesis_ready |
| MovingAverage | classical | execute | 45 | exact | thesis_ready |
| NearestInterpolation | classical | execute | 45 | exact | thesis_ready |
| SeasonalNaive | classical | execute | 45 | exact | thesis_ready |
| BackwardFill | classical | execute | 45 | exact | thesis_ready |
| ExponentialSmoothing | classical | execute | 45 | exact | thesis_ready |

## 4. Scientific DL lifecycle masking truth (aggregate)

All 8 DL/proposed algorithms were evaluated under the train-validate-test lifecycle. Test-phase results are present for all missingness scenarios.

| Algorithm | Family | Phase | Results | Thesis Status |
|---|---|---|---|---|
| Simple RNN | dl_temporal | test | 45 | diagnostic_only |
| Simple LSTM | dl_temporal | test | 45 | diagnostic_only |
| Simple GRU | dl_temporal | test | 45 | diagnostic_only |
| GRU-D | dl_temporal | test | 45 | diagnostic_only |
| BRITS | dl_temporal | test | 45 | diagnostic_only |
| SAITS | dl_temporal | test | 45 | diagnostic_only |
| SAITS-LC | dl_temporal | test | 45 | diagnostic_only |
| SAITS-LCH | dl_temporal | test | 45 | diagnostic_only |

> Validate-phase results for DL algorithms are not applicable for masking truth (no artificial mask is injected during validation); they carry `not_applicable` status.

## 5. Mask artifact evidence status

| Evidence status | Count |
|---|---|
| exact | 765 |
| legacy_fallback | 0 |
| missing | 72 |

## 6. Prediction artifact evidence status

| Evidence status | Count |
|---|---|
| exact | 765 |
| missing | 72 |

## 7. Results blocked from thesis evidence

*No blocked results.*

## 8. Results degraded to diagnostic-only

*No degraded results.*

## 9. Results thesis-ready for visual evidence (aggregate)

405 results across 9 classical/baseline algorithms meet the `thesis_ready` masking truth standard (exact mask evidence, single_shot lifecycle, no natural missingness).

| Algorithm | Count |
|---|---|
| LOCF | 45 |
| LinearInterpolation | 45 |
| ExponentialSmoothing | 45 |
| BackwardFill | 45 |
| SeasonalNaive | 45 |
| NearestInterpolation | 45 |
| MovingAverage | 45 |
| Median | 45 |
| Mean | 45 |

> For individual result UUIDs, refer to the full audit JSON at `masking_truth_status.json`.
