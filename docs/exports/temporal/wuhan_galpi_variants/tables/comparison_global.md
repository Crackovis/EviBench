*Table 0.1: Primary per-recipe comparison under the selected benchmark contract.*

| Station | Recipe | GALPI_V1 | GALPI_V2 | GALPI_V3 | GALPI_V4 | GALPI_V5 | GALPI_V6 | galpi | linear_interpolation |
|---|---|---|---|---|---|---|---|---|---|
| full_grid | MAR 50% | 16.0084 ± 0.3820 (n=20) | 16.6395 ± 0.3217 (n=20) | 16.5849 ± 0.3127 (n=20) | 17.7338 ± 0.4096 (n=20) | 16.0084 ± 0.3820 (n=20) | 17.3226 ± 0.3511 (n=20) | 16.7447 ± 0.3687 (n=20) | **13.2249 ± 0.4309 (n=20)** |
| full_grid | MCAR 50% | 8.7194 ± 0.0518 (n=20) | 9.9202 ± 0.0522 (n=20) | 9.9020 ± 0.0524 (n=20) | 11.9896 ± 0.0503 (n=20) | 8.7194 ± 0.0518 (n=20) | 11.1542 ± 0.0527 (n=20) | 9.9201 ± 0.0521 (n=20) | **7.6956 ± 0.0369 (n=20)** |
| full_grid | MNAR 50% | 11.8479 ± 0.2265 (n=20) | 13.6704 ± 0.2102 (n=20) | 13.6668 ± 0.2096 (n=20) | 17.5402 ± 0.1915 (n=20) | 11.8479 ± 0.2265 (n=20) | 16.0468 ± 0.2035 (n=20) | 13.6704 ± 0.2102 (n=20) | **10.2320 ± 0.0943 (n=20)** |

Primary metric: `rmse` (lower is better). Cells show mean ± std (n). `—` = unavailable; `†` = not comparable; **bold** = gated BEST.
