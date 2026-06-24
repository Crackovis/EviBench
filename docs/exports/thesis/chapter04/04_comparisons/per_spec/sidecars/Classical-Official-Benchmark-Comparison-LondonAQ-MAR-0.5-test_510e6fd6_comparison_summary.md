# Per-spec comparison summary — Classical Official Benchmark Comparison — LondonAQ — MAR 0.5 — test

- Spec ID: `510e6fd6-ef3a-41e2-8755-88ead316a61d`
- Ranking metric: `mae_global`
- Distribution metric shown: `mae`

| Rank | Algorithm | Family | n | Mean | Median | Std | 95% CI | Min | Max |
|---:|---|---|---:|---:|---:|---:|---:|---:|---:|
| 1 | LinearInterpolation | naive | 5 | 3.1063 | 3.7183 | 0.8412 | 0.7374 | 2.0662 | 3.9222 |
| 2 | NearestInterpolation | classical | 5 | 3.5558 | 4.1695 | 0.9163 | 0.8032 | 2.4196 | 4.4982 |
| 3 | ExponentialSmoothing | classical | 5 | 4.0241 | 4.7354 | 1.1144 | 0.9768 | 2.6673 | 5.2071 |
| 4 | MovingAverage | classical | 5 | 4.0776 | 4.7949 | 1.1071 | 0.9704 | 2.7201 | 5.2430 |
| 5 | BackwardFill | classical | 5 | 4.0861 | 4.8085 | 1.1455 | 1.0041 | 2.6483 | 5.3034 |
| 6 | LOCF | naive | 5 | 4.1154 | 4.8287 | 1.1013 | 0.9653 | 2.7518 | 5.2774 |
| 7 | SeasonalNaive | classical | 5 | 5.5624 | 5.8495 | 0.8082 | 0.7084 | 4.5300 | 6.7049 |
| 8 | Mean | naive | 5 | 14.8716 | 15.6923 | 1.4886 | 1.3048 | 12.6024 | 16.2609 |
| 9 | Median | naive | 5 | 14.9202 | 15.2475 | 1.6716 | 1.4652 | 12.3834 | 16.6873 |
