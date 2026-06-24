# Per-spec comparison summary — Classical Official Benchmark Comparison — LondonAQ — MAR 0.1 — test

- Spec ID: `e68132b9-78c0-4fb9-93e8-ba071176460e`
- Ranking metric: `mae_global`
- Distribution metric shown: `mae`

| Rank | Algorithm | Family | n | Mean | Median | Std | 95% CI | Min | Max |
|---:|---|---|---:|---:|---:|---:|---:|---:|---:|
| 1 | LinearInterpolation | naive | 5 | 2.0137 | 2.0380 | 0.1040 | 0.0911 | 1.8851 | 2.1788 |
| 2 | ExponentialSmoothing | classical | 5 | 2.0939 | 2.1371 | 0.1197 | 0.1049 | 1.9464 | 2.2683 |
| 3 | MovingAverage | classical | 5 | 2.1607 | 2.2063 | 0.1221 | 0.1070 | 2.0040 | 2.3410 |
| 4 | NearestInterpolation | classical | 5 | 2.3153 | 2.3515 | 0.1206 | 0.1057 | 2.1612 | 2.4973 |
| 5 | LOCF | naive | 5 | 2.3338 | 2.3671 | 0.1243 | 0.1090 | 2.1727 | 2.5218 |
| 6 | BackwardFill | classical | 5 | 2.3407 | 2.3815 | 0.1352 | 0.1185 | 2.1823 | 2.5421 |
| 7 | SeasonalNaive | classical | 5 | 5.5737 | 5.6255 | 0.4110 | 0.3602 | 5.1368 | 6.2841 |
| 8 | Median | naive | 5 | 12.8983 | 12.9732 | 1.0337 | 0.9061 | 11.2973 | 14.5277 |
| 9 | Mean | naive | 5 | 13.5296 | 13.5913 | 1.1283 | 0.9890 | 12.1779 | 15.5054 |
