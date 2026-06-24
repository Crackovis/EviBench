# Classical Official Benchmark Comparison — LondonAQ — MAR 0.3 — test
> **Scope:** comparison  |  **Status:** incomplete  |  **Grade:** exploratory
> **Spec:** `6e61d78f-e8fd-41a7-9d46-d05ad2e305dc`  |  **Dataset:** `fa9c7108-176e-4c68-840d-6acf3a3e59e4`  |  **Contract:** `7e21e4e5-4a30-4d84-9ef4-825253284754`
> **Generated:** 20260509235948

## Missing Evidence

- Training Curves (training_curve)
- Training Loop Schedule Table (training_loop_schedule_table)

## Experimental Context
*Dataset, missingness mechanism, and mask bank characterization.*  (status: ready)

- Dataset ID: fa9c7108-176e-4c68-840d-6acf3a3e59e4
- Missingness mechanism: mar
- Missingness rate: 30.00%
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
| ms_e27a0ef1-0535-49e0-95ec-83d30b05a85b_realized_rate_bar_chart | Realized Rate Comparison | available | `docs\exports\thesis\chapter04\02_missingness_characterization\figures\London-AQ-Synthetic_fa9c7108\realized_rate_bar_chart_mar_0p1_test.png` |
| ms_e27a0ef1-0535-49e0-95ec-83d30b05a85b_realized_rate_distribution | Realized Rate Distribution | available | `docs\exports\thesis\chapter04\02_missingness_characterization\figures\London-AQ-Synthetic_fa9c7108\realized_rate_distribution_mar_0p1_test.png` |

### Tables

| Slot | Title | Status |
|------|-------|--------|
| mask_bank_summary_table | Mask Bank Summary Table | available |
| mask_bank_summary_table | Mask Bank Summary Table | available |

## Compared Methods
*Algorithm set, families, execution classes, and contract parity.*  (status: ready)

- Algorithm count: 9
- Families: classical
- Execution classes: classical
- Benchmark contract parity: confirmed

### Figures

| Slot | Title | Status | Path |
|------|-------|--------|------|
| comparison_ranking | Algorithm Ranking | available | `docs\exports\thesis\chapter04\04_comparisons\per_spec\figures\Classical-Official-Benchmark-Comparison-LondonAQ-MAR-0.3-test_6e61d78f_comparison_ranking.png` |

### Tables

| Slot | Title | Status |
|------|-------|--------|
| algorithm_metric_ranking | Algorithm Metric Ranking | available |

## Training Evidence
*Training convergence and selection evidence for DL/ML results.*  (status: not_applicable)

- Training evidence pack service unavailable or V7 pack not built.

### Figures

| Slot | Title | Status | Path |
|------|-------|--------|------|
| training_curve | Training Curves | missing | `` |

## Training Missingness Schedule
*Training loop schedule evidence: uniform or custom missingness protocol across training loops.*  (status: incomplete)

- Training curriculum mode: unknown
- Maximum training curriculum loops: unknown
- Selected loop index: unknown

### Tables

| Slot | Title | Status |
|------|-------|--------|
| training_loop_schedule_table | Training Loop Schedule Table | missing |

## Benchmark Results
*Primary metric, ranking, and distribution evidence.*  (status: ready)

- Primary metric: mae_global
- Ranking scope: algorithm
- Realization scope: shared_contract_ready

### Figures

| Slot | Title | Status | Path |
|------|-------|--------|------|
| comparison_ranking | Algorithm Ranking | available | `docs\exports\thesis\chapter04\04_comparisons\per_spec\figures\Classical-Official-Benchmark-Comparison-LondonAQ-MAR-0.3-test_6e61d78f_comparison_ranking.png` |
| comparison_distribution | Metric Distribution | available | `docs\exports\thesis\chapter04\04_comparisons\per_spec\figures\Classical-Official-Benchmark-Comparison-LondonAQ-MAR-0.3-test_6e61d78f_comparison_distribution.png` |

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
| comparison_claim_table | Comparison Claim Table | available | `docs\exports\thesis\chapter04\04_comparisons\per_spec\figures\Classical-Official-Benchmark-Comparison-LondonAQ-MAR-0.3-test_6e61d78f_comparison_ranking.png` |

### Tables

| Slot | Title | Status |
|------|-------|--------|
| comparison_claim_table | Comparison Claim Table | available |

## Reproducibility
*Figure/sidecar availability, artifact catalog status, and git timestamp.*  (status: ready)

- Figures available: 11
- Sidecars available: 0
- Artifact catalog: not indexed
- Git commit timestamp: not available

### Tables

| Slot | Title | Status |
|------|-------|--------|
| reproducibility_table | Reproducibility Table | available |

## Recommended Next Actions
- Generate missing figures and sidecars to reach export-ready state.
