*Table 0.1: Primary per-recipe comparison under the selected benchmark contract.*

| Station | Recipe | cubic_spline | galpi | linear_interpolation | nearest_interpolation |
|---|---|---|---|---|---|
| full_grid | MAR 10% | **5.3982 ± 0.2239 (n=5)** | 5.4847 ± 0.2370 (n=5) | 5.3989 ± 0.2364 (n=5) | 8.5242 ± 0.2313 (n=5) |
| full_grid | MAR 30% | 7.4363 ± 0.3525 (n=5) | 8.8595 ± 0.3341 (n=5) | **7.1430 ± 0.3182 (n=5)** | 9.8552 ± 0.2905 (n=5) |
| full_grid | MAR 50% | 19.5414 ± 2.6500 (n=5) | 16.7084 ± 0.1355 (n=5) | **13.3105 ± 0.4410 (n=5)** | 15.4781 ± 0.4316 (n=5) |
| full_grid | MCAR 10% | 5.1084 ± 0.0331 (n=5) | 5.1422 ± 0.0262 (n=5) | **5.1080 ± 0.0297 (n=5)** | 8.2925 ± 0.0420 (n=5) |
| full_grid | MCAR 30% | 6.2541 ± 0.0382 (n=5) | 6.7225 ± 0.0530 (n=5) | **6.0715 ± 0.0312 (n=5)** | 8.8943 ± 0.0244 (n=5) |
| full_grid | MCAR 50% | 8.1425 ± 0.0450 (n=5) | 9.9044 ± 0.0309 (n=5) | **7.6931 ± 0.0209 (n=5)** | 10.2899 ± 0.0215 (n=5) |
| full_grid | MNAR 10% | **6.8065 ± 0.0824 (n=5)** | 7.1017 ± 0.0940 (n=5) | 7.0710 ± 0.1072 (n=5) | 12.1189 ± 0.1277 (n=5) |
| full_grid | MNAR 30% | **7.6809 ± 0.0590 (n=5)** | 8.8985 ± 0.1870 (n=5) | 8.1237 ± 0.1143 (n=5) | 12.7267 ± 0.1047 (n=5) |
| full_grid | MNAR 50% | **9.1625 ± 0.0807 (n=5)** | 13.6703 ± 0.1602 (n=5) | 10.2114 ± 0.0885 (n=5) | 14.2335 ± 0.0918 (n=5) |

Primary metric: `rmse` (lower is better). Cells show mean ± std (n). `—` = unavailable; `†` = not comparable; **bold** = gated BEST.
