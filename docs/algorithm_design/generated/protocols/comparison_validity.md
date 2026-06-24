# Comparison Validity Protocol (AD3)

This protocol defines how ImputeBench audits comparison validity: ensuring that algorithms compared in a benchmark share the same contract, mask bank, evaluation fingerprints, and realization context.

## Scope

AD3 audits every comparison unit to verify benchmark parity: all algorithms in a comparison must share the same benchmark contract, mask bank, and realization. It also checks evaluation support fingerprints and detects mock/fallback results.

## Status categories

| Status | Meaning |
| --- | --- |
| **thesis_grade** | All parity and evidence requirements met. |
| **valid_exploratory** | Comparison is valid for exploration but not thesis-grade. |
| **warning** | Minor issues — still usable with caveats. |
| **invalid** | Parity violation or missing evidence. |
| **not_evaluated** | Unit not yet evaluated. |

## Parity dimensions

- **Benchmark contract**: same dataset, phases, and metrics.
- **Mask bank**: identical missingness patterns across algorithms.
- **Realization**: same random seed / experimental realization.
- **Evaluation fingerprint**: same evaluation pipeline.

## Mock / fallback detection

AD3 cross-references AD2 execution truth to detect mock-mode executions and fallback results. Units flagged as mock/fallback are marked invalid regardless of other parity checks.
