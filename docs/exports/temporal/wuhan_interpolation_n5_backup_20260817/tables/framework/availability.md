# Metric availability

| Metric | Status | Source quality | Available | Expected | Coverage | Reasons |
|---|---|---|---|---|---|---|
| accuracy | available | exported_primary_metric | 9 | 9 | 1.00 | — |
| speed | available | native_timing_spans | 180 | 180 | 1.00 | — |
| stability | available | exported_primary_metric | 36 | 36 | 1.00 | — |
| rate_robustness | available | exported_primary_metric | 12 | 12 | 1.00 | — |
| mechanism_robustness | available | exported_primary_metric | 12 | 12 | 1.00 | — |
| parameter_efficiency | unavailable | explicit_parameter_evidence | 0 | 180 | 0.00 | no explicit parameter evidence exported |
| memory_efficiency | unavailable | not_instrumented | 0 | 180 | 0.00 | memory is not instrumented; a missing value never becomes 1.0 |
