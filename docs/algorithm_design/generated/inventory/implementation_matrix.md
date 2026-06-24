# Algorithm Implementation Matrix

Developer-readable mapping of every canonical algorithm to its callable contract, configuration keys, dependencies, and test coverage.

_Generated at: 2026-05-09T10:25:35.724528+00:00_

## Implementation matrix

| Algorithm | Source | Manifest | fit | impute | get_info | Config keys | Dependencies | Unit tests | Impl. contract |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| BRITS | plugins\brits\algorithm.py | plugins\brits\imputebench_plugin.json | ✓ | ✓ | ✓ | runtime_mode, hidden_size, learning_rate, epochs, batch_size, require_cuda, verbose | torch, pypots | ✓ | callable |
| Simple GRU | plugins\gru\algorithm.py | plugins\gru\imputebench_plugin.json | ✓ | ✓ | ✓ | runtime_mode, hidden_size, num_layers, dropout, learning_rate, epochs, batch_size, require_cuda | torch | ✗ | callable |
| GRU-D | plugins\gru_d\algorithm.py | plugins\gru_d\imputebench_plugin.json | ✓ | ✓ | ✓ | runtime_mode, hidden_size, dropout, delta_scale, learning_rate, epochs, batch_size, require_cuda | torch | ✗ | callable |
| Simple LSTM | plugins\lstm\algorithm.py | plugins\lstm\imputebench_plugin.json | ✓ | ✓ | ✓ | runtime_mode, hidden_size, num_layers, dropout, learning_rate, epochs, batch_size, require_cuda | torch | ✗ | callable |
| Simple RNN | plugins\rnn\algorithm.py | plugins\rnn\imputebench_plugin.json | ✓ | ✓ | ✓ | runtime_mode, hidden_size, num_layers, dropout, learning_rate, epochs, batch_size, require_cuda | torch | ✗ | callable |
| SAITS | plugins\saits\algorithm.py | plugins\saits\imputebench_plugin.json | ✓ | ✓ | ✓ | runtime_mode, fill_strategy, require_cuda, hidden_size, n_heads, d_ffn | torch, pypots | ✓ | callable |
| SAITS-LC | plugins\saits_lc\algorithm.py | plugins\saits_lc\imputebench_plugin.json | ✓ | ✓ | ✓ | runtime_mode, fill_strategy, require_cuda, hidden_size, n_heads, d_ffn, conditioning_authority, kernel_size, dilation, beta, fusion_mode | pypots | ✓ | callable |
| SAITS-LCH | plugins\saits_lch\algorithm.py | plugins\saits_lch\imputebench_plugin.json | ✓ | ✓ | ✓ | runtime_mode, fill_strategy, require_cuda, hidden_size, n_heads, d_ffn, conditioning_authority, kernel_size, dilation, beta, fusion_mode | pypots | ✗ | callable |
| BackwardFill | plugins\backward_fill\algorithm.py | plugins\backward_fill\imputebench_plugin.json | ✓ | ✓ | ✓ | — | — | ✗ | callable |
| ExponentialSmoothing | plugins\exponential_smoothing\algorithm.py | plugins\exponential_smoothing\imputebench_plugin.json | ✓ | ✓ | ✓ | alpha | — | ✗ | callable |
| MovingAverage | plugins\moving_average\algorithm.py | plugins\moving_average\imputebench_plugin.json | ✓ | ✓ | ✓ | window_size | — | ✗ | callable |
| NearestInterpolation | plugins\nearest_interpolation\algorithm.py | plugins\nearest_interpolation\imputebench_plugin.json | ✓ | ✓ | ✓ | — | — | ✗ | callable |
| SeasonalNaive | plugins\seasonal_naive\algorithm.py | plugins\seasonal_naive\imputebench_plugin.json | ✓ | ✓ | ✓ | seasonal_period | — | ✗ | callable |
| LinearInterpolation | plugins\linear_interpolation\algorithm.py | plugins\linear_interpolation\imputebench_plugin.json | ✓ | ✓ | ✓ | — | — | ✗ | callable |
| LOCF | plugins\locf\algorithm.py | plugins\locf\imputebench_plugin.json | ✓ | ✓ | ✓ | — | — | ✗ | callable |
| Mean | plugins\mean\algorithm.py | plugins\mean\imputebench_plugin.json | ✓ | ✓ | ✓ | — | — | ✗ | callable |
| Median | plugins\median\algorithm.py | plugins\median\imputebench_plugin.json | ✓ | ✓ | ✓ | — | — | ✗ | callable |

> **Unit tests vs execution runs:** The 'Unit tests' column shows dedicated test files in `tests/`. An `✗` here does NOT mean the algorithm was never tested — all 17 algorithms have been executed through the benchmark pipeline with 45 (classical/baselines) or 63 (DL) results each. Execution testing is confirmed by AD2 (see `generated/status/execution_truth.md`). Unit test files are a supplementary quality measure, not a gate for scientific validity.
