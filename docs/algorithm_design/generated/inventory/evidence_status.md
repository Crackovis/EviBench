# Algorithm Evidence Status

Evidence-focused readiness matrix showing AD2 execution truth evidence, AD3 comparison evidence, and resolved audit status for every canonical algorithm.

_Generated at: 2026-05-09T10:25:35.724528+00:00_

## Evidence status matrix

| Algorithm | AD2 verdict | Checkpoint | Train history | Pred. fingerprint | Results | Phases | AD3 status | **Resolved** |
| --- | --- | --- | --- | --- | ---:| --- | --- | --- |
| BRITS | execution_ready | ✓ | ✓ | ✓ | 84 | test, train, validate | exploratory_only | **execution_ready** |
| Simple GRU | execution_ready | ✓ | ✓ | ✓ | 85 | test, train, validate | exploratory_only | **execution_ready** |
| GRU-D | execution_ready | ✓ | ✓ | ✓ | 84 | test, train, validate | exploratory_only | **execution_ready** |
| Simple LSTM | execution_ready | ✓ | ✓ | ✓ | 85 | test, train, validate | exploratory_only | **execution_ready** |
| Simple RNN | execution_ready | ✓ | ✓ | ✓ | 85 | test, train, validate | exploratory_only | **execution_ready** |
| SAITS | execution_ready | ✓ | ✓ | ✓ | 84 | test, train, validate | exploratory_only | **execution_ready** |
| SAITS-LC | execution_ready | ✓ | ✓ | ✓ | 84 | test, train, validate | exploratory_only | **execution_ready** |
| SAITS-LCH | execution_ready | ✓ | ✓ | ✓ | 84 | test, train, validate | exploratory_only | **execution_ready** |
| BackwardFill | execution_ready | — | — | ✓ | 45 | execute | exploratory_only | **execution_ready** |
| ExponentialSmoothing | execution_ready | — | — | ✓ | 45 | execute | exploratory_only | **execution_ready** |
| MovingAverage | execution_ready | — | — | ✓ | 45 | execute | exploratory_only | **execution_ready** |
| NearestInterpolation | execution_ready | — | — | ✓ | 45 | execute | exploratory_only | **execution_ready** |
| SeasonalNaive | execution_ready | — | — | ✓ | 45 | execute | exploratory_only | **execution_ready** |
| LinearInterpolation | execution_ready | — | — | ✓ | 45 | execute | exploratory_only | **execution_ready** |
| LOCF | execution_ready | — | — | ✓ | 45 | execute | exploratory_only | **execution_ready** |
| Mean | execution_ready | — | — | ✓ | 45 | execute | exploratory_only | **execution_ready** |
| Median | execution_ready | — | — | ✓ | 45 | execute | exploratory_only | **execution_ready** |

## Column notes

- **Checkpoint, Train history, Pred. fingerprint**: Only expected for DL algorithms (RNN, LSTM, GRU, BRITS, SAITS, SAITS-LC, SAITS-LCH). Baselines and classical methods do not produce checkpoints — their execution evidence consists of metrics and timing data.
- **Results**: Number of execution results in the benchmark database.
- **Phases**: Execution phases covered (train, validate, test).
