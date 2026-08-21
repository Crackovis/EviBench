# st_storyboard_within_st

- lot: within_st
- dataset_id: c1b03ed1-dad4-444b-a06d-e85cb81378b6
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
