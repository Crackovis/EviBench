# Comparison Validity Status (AD3)

AD3 is the **thesis-grade gate**. While AD2 verifies that each algorithm was executed correctly, AD3 verifies that their results can be compared fairly in a publication-ready benchmark.

_Generated at: 2026-05-09T10:25:35.724528+00:00_

## Materialization status

- **Active specs:** 18
- **Materialized:** 18
- **Not materialized:** 0
- **Overall verdict:** `materialized`

| Spec ID | Profile | Units | Status |
| --- | --- | ---:| --- |
| 0b1af9df-a5b... | strict_scientific | 45 | ✅ materialized |
| 24121bbb-e34... | strict_scientific | 40 | ✅ materialized |
| 249d9bb3-b3c... | strict_scientific | 40 | ✅ materialized |
| 34b964ff-bcc... | strict_scientific | 40 | ✅ materialized |
| 3add59da-9ad... | strict_scientific | 45 | ✅ materialized |
| 3cf4150a-559... | strict_scientific | 45 | ✅ materialized |
| 458a1870-020... | strict_scientific | 45 | ✅ materialized |
| 510e6fd6-ef3... | strict_scientific | 45 | ✅ materialized |
| 5834ca26-669... | strict_scientific | 40 | ✅ materialized |
| 6e61d78f-e8f... | strict_scientific | 45 | ✅ materialized |
| 8b71e93a-d3b... | strict_scientific | 40 | ✅ materialized |
| a1819411-a74... | strict_scientific | 45 | ✅ materialized |
| cd369f92-92f... | strict_scientific | 40 | ✅ materialized |
| cfa26b82-df7... | strict_scientific | 40 | ✅ materialized |
| d05fcf94-99d... | strict_scientific | 45 | ✅ materialized |
| e68132b9-78c... | strict_scientific | 45 | ✅ materialized |
| f72ca31a-a6c... | strict_scientific | 40 | ✅ materialized |
| f9bea2bd-1ab... | strict_scientific | 40 | ✅ materialized |


## What AD3 checks

A comparison is **thesis-grade** only when all compared units share the same scientific frame:

| Requirement | Description |
| --- | --- |
| Same dataset | All results from the same source dataset |
| Same lifecycle phase | Typically `test` for official comparisons |
| Same benchmark contract | Identical contract ID across all units |
| Same mask bank | Identical mask bank ID |
| Same evaluation support | Identical evaluation_support_fingerprint |
| Same missingness family/rate | Consistent missingness protocol |
| Same metric scope | Global, pollutant, or node-level metrics |
| Same ranking metric | The ranking metric exists for every unit |
| Same realization policy | Each algorithm covers the same set of realizations |
| Fresh result fingerprints | No stale cache |
| No mock/fallback execution | Every result uses genuine execution |

## Exploratory vs thesis-grade

Currently, all 18 benchmark comparisons (Classical and DL, across MCAR/MAR/MNAR at 10%/30%/50%) are marked **exploratory_only**. This is the *correct* status — it reflects where we are in the benchmark lifecycle, not a problem with the algorithms.

| Aspect | Exploratory | Thesis-grade |
| --- | --- | --- |
| Dataset alignment | Recommended | Required |
| Phase alignment | Recommended | Required (usually `test`) |
| Benchmark contract | Not required | Required and shared |
| Mask bank parity | Not required | Required |
| Evaluation support | Not required | Required |
| Execution truth | Informational | Required `execution_ready` |
| Cache freshness | Informational | Required non-stale |

### Why comparisons are currently exploratory

The benchmark infrastructure is fully in place (all 17 algorithms are execution_ready, benchmark contracts exist, mask banks are shared). Promotion to thesis-grade requires:

1. **Full MCAR/MAR/MNAR coverage** across all missingness rates (10%, 30%, 50%) — the comparisons exist but need audit for cache freshness and claim policy.
2. **AD3 audit completion** — claim policy integration, cache freshness verification, and realization coverage checks must pass for every spec.
3. **Evidence claim policy** — claims like 'best' or 'superior' are blocked; only descriptive claims are allowed until thesis-grade promotion.

> **Key insight:** AD3 being `exploratory_only` does NOT mean the algorithms are broken. AD2 already confirmed 17/17 execution_ready. AD3 is a *different* question: can we publish a comparison table with scientific confidence?

## AD3 is independent from execution truth

AD3 and AD2 answer different questions:

| Question | Answered by |
| --- | --- |
| Did the algorithm execute correctly? | AD2 (execution truth) |
| Can its results be compared fairly? | AD3 (comparison validity) |

An algorithm can be **execution_ready** (AD2 confirmed) while its comparisons remain **exploratory** (AD3 not yet thesis-grade). This is normal — thesis-grade comparison requires all units to pass every parity check simultaneously, which is a higher bar.

## Per-algorithm comparison status

Comparison validity status for each canonical algorithm. `exploratory_only` = can be compared for exploration; `thesis_grade_ready` = can be included in publication-ready tables.

| Algorithm | AD3 Status | AD2 Verdict | Resolved |
| --- | --- | --- | --- |
| BRITS | exploratory_only | execution_ready | **execution_ready** |
| Simple GRU | exploratory_only | execution_ready | **execution_ready** |
| GRU-D | exploratory_only | execution_ready | **execution_ready** |
| Simple LSTM | exploratory_only | execution_ready | **execution_ready** |
| Simple RNN | exploratory_only | execution_ready | **execution_ready** |
| SAITS | exploratory_only | execution_ready | **execution_ready** |
| SAITS-LC | exploratory_only | execution_ready | **execution_ready** |
| SAITS-LCH | exploratory_only | execution_ready | **execution_ready** |
| BackwardFill | exploratory_only | execution_ready | **execution_ready** |
| ExponentialSmoothing | exploratory_only | execution_ready | **execution_ready** |
| MovingAverage | exploratory_only | execution_ready | **execution_ready** |
| NearestInterpolation | exploratory_only | execution_ready | **execution_ready** |
| SeasonalNaive | exploratory_only | execution_ready | **execution_ready** |
| LinearInterpolation | exploratory_only | execution_ready | **execution_ready** |
| LOCF | exploratory_only | execution_ready | **execution_ready** |
| Mean | exploratory_only | execution_ready | **execution_ready** |
| Median | exploratory_only | execution_ready | **execution_ready** |

## Path to thesis-grade promotion

To promote comparisons to thesis-grade:

```bash
# 1. Verify cache freshness for all comparison specs
imputebench audit comparison-validity --force-refresh-check

# 2. Run evidence claim policy audit
imputebench evidence gate comparison <spec_id> --profile thesis_ready

# 3. Verify thesis gates
imputebench thesis gates
```

Once all checks pass, the comparison spec is promoted from `exploratory_only` to `thesis_grade_ready`.
