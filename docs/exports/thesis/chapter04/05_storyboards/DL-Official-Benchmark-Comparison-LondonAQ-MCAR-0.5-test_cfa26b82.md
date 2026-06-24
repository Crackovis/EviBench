# DL Official Benchmark Comparison — LondonAQ — MCAR 0.5 — test
> **Scope:** comparison  |  **Status:** incomplete  |  **Grade:** exploratory
> **Spec:** `cfa26b82-df78-4f03-bda8-8bd588f3a7c8`  |  **Dataset:** `fa9c7108-176e-4c68-840d-6acf3a3e59e4`  |  **Contract:** `55ffafa0-3408-401c-8523-67b71faa4d80`
> **Generated:** 20260510001100

## Experimental Context
*Dataset, missingness mechanism, and mask bank characterization.*  (status: ready)

- Dataset ID: fa9c7108-176e-4c68-840d-6acf3a3e59e4
- Missingness mechanism: mcar
- Missingness rate: 50.00%
- Benchmark realization count: 5
- Benchmark realizations are distinct from training curriculum loops.
- Shared support fingerprints: 1

### Figures

| Slot | Title | Status | Path |
|------|-------|--------|------|
| ds_fa9c7108-176e-4c68-840d-6acf3a3e59e4_dataset_dataset_summary_table | Dataset Summary Table | available | `docs\exports\thesis\chapter04\01_dataset_characterization\figures\dataset_summary_table.png` |
| ds_fa9c7108-176e-4c68-840d-6acf3a3e59e4_dataset_temporal_coverage_strip | Temporal Coverage Strip | available | `docs\exports\thesis\chapter04\01_dataset_characterization\figures\temporal_coverage_strip.png` |
| ds_fa9c7108-176e-4c68-840d-6acf3a3e59e4_dataset_pollutant_distribution_panel | Pollutant Distribution Panel | available | `docs\exports\thesis\chapter04\01_dataset_characterization\figures\pollutant_distribution_panel.png` |
| ds_fa9c7108-176e-4c68-840d-6acf3a3e59e4_dataset_pollutant_sample_timeseries | Pollutant Sample Timeseries | available | `docs\exports\thesis\chapter04\01_dataset_characterization\figures\pollutant_sample_timeseries.png` |
| ds_fa9c7108-176e-4c68-840d-6acf3a3e59e4_dataset_spatial_grid_snapshot | Spatial Grid Snapshot | available | `docs\exports\thesis\chapter04\01_dataset_characterization\figures\spatial_grid_snapshot.png` |
| ms_ab3eea8d-60ba-4514-9a83-fdb043c7340e_realized_rate_bar_chart | Realized Rate Comparison | available | `docs\exports\thesis\chapter04\02_missingness_characterization\figures\London-AQ-Synthetic_fa9c7108\realized_rate_bar_chart_mar_0p1_test.png` |
| ms_ab3eea8d-60ba-4514-9a83-fdb043c7340e_realized_rate_distribution | Realized Rate Distribution | available | `docs\exports\thesis\chapter04\02_missingness_characterization\figures\London-AQ-Synthetic_fa9c7108\realized_rate_distribution_mar_0p1_test.png` |

### Tables

| Slot | Title | Status |
|------|-------|--------|
| mask_bank_summary_table | Mask Bank Summary Table | available |
| mask_bank_summary_table | Mask Bank Summary Table | available |

## Compared Methods
*Algorithm set, families, execution classes, and contract parity.*  (status: ready)

- Algorithm count: 8
- Families: dl_temporal
- Execution classes: scientific_dl
- Benchmark contract parity: confirmed

### Figures

| Slot | Title | Status | Path |
|------|-------|--------|------|
| comparison_ranking | Algorithm Ranking | available | `docs\exports\thesis\chapter04\04_comparisons\per_spec\figures\DL-Official-Benchmark-Comparison-LondonAQ-MCAR-0.5-test_cfa26b82_comparison_ranking.png` |

### Tables

| Slot | Title | Status |
|------|-------|--------|
| algorithm_metric_ranking | Algorithm Metric Ranking | available |

## Training Evidence
*Training convergence and selection evidence for DL/ML results.*  (status: available)

- Training histories detected via direct result inspection.

### Figures

| Slot | Title | Status | Path |
|------|-------|--------|------|
| training_curve | Training Curves | available | `data\artifacts\651a78d3-b5ad-4250-8e83-7fedfa86ce54\8cb5f071-edb8-4d00-9e53-0ae2a8c871de\history\training_history_2026-05-08T233434.json` |

## Training Missingness Schedule
*Training loop schedule evidence: uniform or custom missingness protocol across training loops.*  (status: ready)

- Training curriculum mode: custom
- Maximum training curriculum loops: 3
- Selected loop index: 1

### Claims

- ✅ **training_schedule_claim**: The final checkpoint was selected after 3 train/internal-validation loops under a [custom] missingness schedule.

### Tables

| Slot | Title | Status |
|------|-------|--------|
| training_loop_schedule_table | Training Loop Schedule Table | available |

## Benchmark Results
*Primary metric, ranking, and distribution evidence.*  (status: ready)

- Primary metric: mae_global
- Ranking scope: algorithm
- Realization scope: shared_contract_ready

### Figures

| Slot | Title | Status | Path |
|------|-------|--------|------|
| comparison_ranking | Algorithm Ranking | available | `docs\exports\thesis\chapter04\04_comparisons\per_spec\figures\DL-Official-Benchmark-Comparison-LondonAQ-MCAR-0.5-test_cfa26b82_comparison_ranking.png` |
| comparison_distribution | Metric Distribution | available | `docs\exports\thesis\chapter04\04_comparisons\per_spec\figures\DL-Official-Benchmark-Comparison-LondonAQ-MCAR-0.5-test_cfa26b82_comparison_distribution.png` |

### Tables

| Slot | Title | Status |
|------|-------|--------|
| algorithm_metric_ranking | Algorithm Metric Ranking | available |

## Claims and Caveats
*Claims taxonomy from V3 schema claim profile and export policy.*  (status: usable_with_caveats)

- Claim level: exploratory
- Allowed claims: metric values may be inspected, artifact availability may be reported, result cohort may be described
- Blocked claims: algorithm X is best, deep learning is superior, classical methods are superior, SAITS outperforms all baselines, result is generalizable beyond this dataset, ranking superiority, family superiority, generalization, thesis-ready conclusion

### Figures

| Slot | Title | Status | Path |
|------|-------|--------|------|
| comparison_claim_table | Comparison Claim Table | available | `docs\exports\thesis\chapter04\04_comparisons\per_spec\figures\DL-Official-Benchmark-Comparison-LondonAQ-MCAR-0.5-test_cfa26b82_comparison_ranking.png` |

### Tables

| Slot | Title | Status |
|------|-------|--------|
| comparison_claim_table | Comparison Claim Table | available |

## Reproducibility
*Figure/sidecar availability, artifact catalog status, and git timestamp.*  (status: ready)

- Figures available: 12
- Sidecars available: 0
- Artifact catalog: not indexed
- Git commit timestamp: not available

### Tables

| Slot | Title | Status |
|------|-------|--------|
| reproducibility_table | Reproducibility Table | available |

## Recommended Next Actions
- Generate missing figures and sidecars to reach export-ready state.
