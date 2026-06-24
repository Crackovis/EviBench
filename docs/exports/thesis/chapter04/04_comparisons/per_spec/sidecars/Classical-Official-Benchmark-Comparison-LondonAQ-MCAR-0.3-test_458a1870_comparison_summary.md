# Per-spec comparison summary — Classical Official Benchmark Comparison — LondonAQ — MCAR 0.3 — test

- Spec ID: `458a1870-020e-4bb6-940f-e0394005aee8`
- Ranking metric: `mae_global`
- Distribution metric shown: `mae`

| Rank | Algorithm | Family | n | Mean | Median | Std | 95% CI | Min | Max |
|---:|---|---|---:|---:|---:|---:|---:|---:|---:|
| 1 | LinearInterpolation | naive | 5 | 1.8574 | 1.8568 | 0.0046 | 0.0041 | 1.8508 | 1.8653 |
| 2 | ExponentialSmoothing | classical | 5 | 1.9282 | 1.9263 | 0.0060 | 0.0053 | 1.9209 | 1.9375 |
| 3 | MovingAverage | classical | 5 | 2.0320 | 2.0324 | 0.0074 | 0.0065 | 2.0207 | 2.0434 |
| 4 | NearestInterpolation | classical | 5 | 2.1185 | 2.1183 | 0.0047 | 0.0041 | 2.1126 | 2.1262 |
| 5 | BackwardFill | classical | 5 | 2.1377 | 2.1373 | 0.0068 | 0.0060 | 2.1261 | 2.1450 |
| 6 | LOCF | naive | 5 | 2.1401 | 2.1411 | 0.0061 | 0.0053 | 2.1313 | 2.1497 |
| 7 | SeasonalNaive | classical | 5 | 4.1957 | 4.1905 | 0.0238 | 0.0209 | 4.1650 | 4.2238 |
| 8 | Median | naive | 5 | 10.5983 | 10.5810 | 0.0320 | 0.0280 | 10.5609 | 10.6479 |
| 9 | Mean | naive | 5 | 11.4005 | 11.3934 | 0.0181 | 0.0159 | 11.3861 | 11.4357 |
