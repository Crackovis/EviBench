# Per-spec comparison summary — Classical Official Benchmark Comparison — LondonAQ — MNAR 0.3 — test

- Spec ID: `0b1af9df-a5b3-4a87-802e-56512f9a1816`
- Ranking metric: `mae_global`
- Distribution metric shown: `mae`

| Rank | Algorithm | Family | n | Mean | Median | Std | 95% CI | Min | Max |
|---:|---|---|---:|---:|---:|---:|---:|---:|---:|
| 1 | LinearInterpolation | naive | 5 | 2.0333 | 2.0340 | 0.0069 | 0.0060 | 2.0207 | 2.0410 |
| 2 | ExponentialSmoothing | classical | 5 | 2.2858 | 2.2862 | 0.0100 | 0.0087 | 2.2710 | 2.2971 |
| 3 | MovingAverage | classical | 5 | 2.3101 | 2.3104 | 0.0107 | 0.0094 | 2.2966 | 2.3243 |
| 4 | NearestInterpolation | classical | 5 | 2.3671 | 2.3677 | 0.0042 | 0.0037 | 2.3619 | 2.3741 |
| 5 | BackwardFill | classical | 5 | 2.4425 | 2.4486 | 0.0150 | 0.0131 | 2.4197 | 2.4627 |
| 6 | LOCF | naive | 5 | 2.4434 | 2.4434 | 0.0089 | 0.0078 | 2.4336 | 2.4569 |
| 7 | SeasonalNaive | classical | 5 | 6.4831 | 6.4664 | 0.0500 | 0.0438 | 6.4309 | 6.5746 |
| 8 | Mean | naive | 5 | 18.6802 | 18.7414 | 0.0882 | 0.0773 | 18.5283 | 18.7573 |
| 9 | Median | naive | 5 | 20.7902 | 20.8357 | 0.0954 | 0.0836 | 20.6149 | 20.8896 |
