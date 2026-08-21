# Metric availability

| Metric | Status | Source quality | Available | Expected | Coverage | Reasons |
|---|---|---|---|---|---|---|
| accuracy | available | exported_primary_metric | 3 | 3 | 1.00 | — |
| speed | available | native_timing_spans | 480 | 480 | 1.00 | — |
| stability | available | exported_primary_metric | 24 | 24 | 1.00 | — |
| rate_robustness | unavailable | exported_primary_metric | 0 | 24 | 0.00 | no comparable rate pairs |
| mechanism_robustness | available | exported_primary_metric | 8 | 8 | 1.00 | — |
| parameter_efficiency | unavailable | explicit_parameter_evidence | 0 | 480 | 0.00 | no explicit parameter evidence exported |
| memory_efficiency | unavailable | not_instrumented | 0 | 480 | 0.00 | memory is not instrumented; a missing value never becomes 1.0 |
