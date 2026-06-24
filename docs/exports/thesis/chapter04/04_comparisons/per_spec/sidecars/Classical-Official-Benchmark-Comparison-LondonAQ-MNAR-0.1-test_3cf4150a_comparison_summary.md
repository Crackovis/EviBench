# Per-spec comparison summary — Classical Official Benchmark Comparison — LondonAQ — MNAR 0.1 — test

- Spec ID: `3cf4150a-5599-4b45-8fe5-272e0df704fb`
- Ranking metric: `mae_global`
- Distribution metric shown: `mae`

| Rank | Algorithm | Family | n | Mean | Median | Std | 95% CI | Min | Max |
|---:|---|---|---:|---:|---:|---:|---:|---:|---:|
| 1 | LinearInterpolation | naive | 5 | 2.0109 | 2.0116 | 0.0067 | 0.0059 | 2.0031 | 2.0221 |
| 2 | ExponentialSmoothing | classical | 5 | 2.1621 | 2.1599 | 0.0086 | 0.0075 | 2.1529 | 2.1744 |
| 3 | MovingAverage | classical | 5 | 2.1753 | 2.1758 | 0.0119 | 0.0104 | 2.1587 | 2.1917 |
| 4 | NearestInterpolation | classical | 5 | 2.3446 | 2.3409 | 0.0085 | 0.0075 | 2.3357 | 2.3603 |
| 5 | BackwardFill | classical | 5 | 2.3577 | 2.3624 | 0.0087 | 0.0076 | 2.3467 | 2.3674 |
| 6 | LOCF | naive | 5 | 2.3584 | 2.3516 | 0.0137 | 0.0120 | 2.3463 | 2.3833 |
| 7 | SeasonalNaive | classical | 5 | 7.0741 | 7.0181 | 0.0903 | 0.0792 | 6.9956 | 7.2334 |
| 8 | Mean | naive | 5 | 18.0772 | 18.0461 | 0.1784 | 0.1563 | 17.8305 | 18.3732 |
| 9 | Median | naive | 5 | 19.9500 | 19.9062 | 0.2191 | 0.1921 | 19.6278 | 20.3098 |
