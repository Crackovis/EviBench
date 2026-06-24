# Per-spec comparison summary — Classical Official Benchmark Comparison — LondonAQ — MCAR 0.1 — test

- Spec ID: `d05fcf94-99db-41af-a218-8a5f7c7ac3b5`
- Ranking metric: `mae_global`
- Distribution metric shown: `mae`

| Rank | Algorithm | Family | n | Mean | Median | Std | 95% CI | Min | Max |
|---:|---|---|---:|---:|---:|---:|---:|---:|---:|
| 1 | LinearInterpolation | naive | 5 | 1.8400 | 1.8408 | 0.0027 | 0.0024 | 1.8350 | 1.8431 |
| 2 | ExponentialSmoothing | classical | 5 | 1.8614 | 1.8630 | 0.0077 | 0.0068 | 1.8487 | 1.8705 |
| 3 | MovingAverage | classical | 5 | 1.9345 | 1.9369 | 0.0083 | 0.0073 | 1.9241 | 1.9433 |
| 4 | NearestInterpolation | classical | 5 | 2.1062 | 2.1072 | 0.0072 | 0.0063 | 2.0949 | 2.1157 |
| 5 | LOCF | naive | 5 | 2.1129 | 2.1172 | 0.0071 | 0.0062 | 2.1019 | 2.1208 |
| 6 | BackwardFill | classical | 5 | 2.1166 | 2.1159 | 0.0064 | 0.0056 | 2.1055 | 2.1234 |
| 7 | SeasonalNaive | classical | 5 | 4.5531 | 4.5405 | 0.0435 | 0.0381 | 4.4973 | 4.6216 |
| 8 | Median | naive | 5 | 10.6137 | 10.6013 | 0.0356 | 0.0312 | 10.5644 | 10.6701 |
| 9 | Mean | naive | 5 | 11.4138 | 11.4054 | 0.0215 | 0.0189 | 11.3900 | 11.4461 |
