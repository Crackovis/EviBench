# Per-spec comparison summary — Classical Official Benchmark Comparison — LondonAQ — MCAR 0.5 — test

- Spec ID: `a1819411-a742-4227-82e0-73d3c81864c6`
- Ranking metric: `mae_global`
- Distribution metric shown: `mae`

| Rank | Algorithm | Family | n | Mean | Median | Std | 95% CI | Min | Max |
|---:|---|---|---:|---:|---:|---:|---:|---:|---:|
| 1 | LinearInterpolation | naive | 5 | 1.8784 | 1.8787 | 0.0024 | 0.0021 | 1.8757 | 1.8822 |
| 2 | ExponentialSmoothing | classical | 5 | 2.0274 | 2.0277 | 0.0046 | 0.0040 | 2.0205 | 2.0339 |
| 3 | NearestInterpolation | classical | 5 | 2.1366 | 2.1365 | 0.0040 | 0.0035 | 2.1315 | 2.1423 |
| 4 | MovingAverage | classical | 5 | 2.1370 | 2.1381 | 0.0046 | 0.0040 | 2.1295 | 2.1421 |
| 5 | BackwardFill | classical | 5 | 2.1921 | 2.1917 | 0.0038 | 0.0034 | 2.1869 | 2.1987 |
| 6 | LOCF | naive | 5 | 2.1939 | 2.1941 | 0.0047 | 0.0041 | 2.1865 | 2.2001 |
| 7 | SeasonalNaive | classical | 5 | 3.9485 | 3.9576 | 0.0182 | 0.0160 | 3.9257 | 3.9708 |
| 8 | Median | naive | 5 | 10.6105 | 10.6032 | 0.0163 | 0.0143 | 10.5975 | 10.6422 |
| 9 | Mean | naive | 5 | 11.3966 | 11.4003 | 0.0085 | 0.0075 | 11.3802 | 11.4046 |
