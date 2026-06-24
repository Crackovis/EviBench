# Per-spec comparison summary — Classical Official Benchmark Comparison — LondonAQ — MNAR 0.5 — test

- Spec ID: `3add59da-9ad3-47ae-ae36-397a10992e53`
- Ranking metric: `mae_global`
- Distribution metric shown: `mae`

| Rank | Algorithm | Family | n | Mean | Median | Std | 95% CI | Min | Max |
|---:|---|---|---:|---:|---:|---:|---:|---:|---:|
| 1 | LinearInterpolation | naive | 5 | 2.2392 | 2.2261 | 0.0246 | 0.0215 | 2.2161 | 2.2798 |
| 2 | NearestInterpolation | classical | 5 | 2.6021 | 2.5900 | 0.0253 | 0.0222 | 2.5777 | 2.6386 |
| 3 | ExponentialSmoothing | classical | 5 | 2.7312 | 2.7142 | 0.0311 | 0.0273 | 2.7063 | 2.7884 |
| 4 | MovingAverage | classical | 5 | 2.7423 | 2.7223 | 0.0318 | 0.0278 | 2.7163 | 2.7987 |
| 5 | BackwardFill | classical | 5 | 2.8132 | 2.8374 | 0.0392 | 0.0344 | 2.7628 | 2.8523 |
| 6 | LOCF | naive | 5 | 2.8404 | 2.8209 | 0.0314 | 0.0275 | 2.8145 | 2.8956 |
| 7 | SeasonalNaive | classical | 5 | 6.3461 | 6.3407 | 0.0299 | 0.0262 | 6.3075 | 6.3991 |
| 8 | Mean | naive | 5 | 19.6709 | 19.6836 | 0.0493 | 0.0432 | 19.5780 | 19.7243 |
| 9 | Median | naive | 5 | 21.6326 | 21.6455 | 0.0572 | 0.0501 | 21.5324 | 21.7068 |
