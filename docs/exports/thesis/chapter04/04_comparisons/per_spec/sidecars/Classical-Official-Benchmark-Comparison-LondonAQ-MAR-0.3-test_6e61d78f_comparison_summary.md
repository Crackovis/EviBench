# Per-spec comparison summary — Classical Official Benchmark Comparison — LondonAQ — MAR 0.3 — test

- Spec ID: `6e61d78f-e8fd-41a7-9d46-d05ad2e305dc`
- Ranking metric: `mae_global`
- Distribution metric shown: `mae`

| Rank | Algorithm | Family | n | Mean | Median | Std | 95% CI | Min | Max |
|---:|---|---|---:|---:|---:|---:|---:|---:|---:|
| 1 | LinearInterpolation | naive | 5 | 2.0438 | 2.0717 | 0.1127 | 0.0988 | 1.9057 | 2.2186 |
| 2 | ExponentialSmoothing | classical | 5 | 2.2745 | 2.3362 | 0.1674 | 0.1467 | 2.0811 | 2.5152 |
| 3 | NearestInterpolation | classical | 5 | 2.3562 | 2.4030 | 0.1390 | 0.1219 | 2.1781 | 2.5652 |
| 4 | MovingAverage | classical | 5 | 2.3613 | 2.4235 | 0.1666 | 0.1460 | 2.1675 | 2.6042 |
| 5 | BackwardFill | classical | 5 | 2.4438 | 2.4983 | 0.1762 | 0.1544 | 2.2324 | 2.7007 |
| 6 | LOCF | naive | 5 | 2.4463 | 2.5014 | 0.1639 | 0.1437 | 2.2472 | 2.6915 |
| 7 | SeasonalNaive | classical | 5 | 4.9798 | 5.0009 | 0.3223 | 0.2825 | 4.5917 | 5.5380 |
| 8 | Median | naive | 5 | 13.2879 | 13.5172 | 1.0425 | 0.9138 | 11.4762 | 14.7016 |
| 9 | Mean | naive | 5 | 13.7809 | 14.0230 | 1.1430 | 1.0018 | 12.2171 | 15.6200 |
