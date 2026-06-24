# st_storyboard_temporal_vs_st_aligned

- lot: temporal_vs_st_aligned
- dataset_id: fa9c7108-176e-4c68-840d-6acf3a3e59e4
- graph_policy: multiple
- graph_fingerprint: multiple
- realization_id: multiple
- mask_family: multiple
- missingness_rate: 

## Allowed claims
- controlled_diagnostic_ranking
- metric_summary_under_fixed_support
- runtime_validated_comparison

## Blocked claims
- comparison_invalid
- deployment_generalization
- diagnostic_only_plugin_dcrnn
- diagnostic_only_plugin_grin
- diagnostic_only_plugin_ignnk
- diagnostic_only_plugin_stgcn
- paper_faithful_model_claim
- thesis_ready_algorithm_superiority
- universal_best_model

## Caveats
- Claims are limited to controlled EviBench runtime evidence.
- No paper-faithful implementation claim is made.
- Plugin dcrnn is diagnostic-only and cannot support thesis-grade claims.
- Plugin grin is diagnostic-only and cannot support thesis-grade claims.
- Plugin ignnk is diagnostic-only and cannot support thesis-grade claims.
- Plugin stgcn is diagnostic-only and cannot support thesis-grade claims.
- Wrappers are diagnostic-only and not marked scientific_ready.
- missing_temporal_or_st_results
