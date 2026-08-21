# Rate robustness (degradation between missingness rates)

Descriptive degradation between strictly comparable cells (same dataset view, station, algorithm, mechanism, phase, metric, recipe lineage). The low and high supports differ, so this dimension stays descriptive.

| Station | Mechanism | Algorithm | Low rate | High rate | RMSE low | RMSE high | Δ abs | Δ rel % | Score | Status |
|---|---|---|---|---|---|---|---|---|---|---|
| full_grid | mar | cubic_spline | 0.10 | 0.50 | 5.3298 | 19.3106 | 13.9808 | 262.31 | 0.000 | available |
| full_grid | mar | galpi | 0.10 | 0.50 | 5.4280 | 16.7447 | 11.3167 | 208.49 | 0.298 | available |
| full_grid | mar | linear_interpolation | 0.10 | 0.50 | 5.3422 | 13.2249 | 7.8827 | 147.56 | 0.635 | available |
| full_grid | mar | nearest_interpolation | 0.10 | 0.50 | 8.4709 | 15.3717 | 6.9008 | 81.46 | 1.000 | available |
| full_grid | mcar | cubic_spline | 0.10 | 0.50 | 5.1102 | 8.1574 | 3.0472 | 59.63 | 0.485 | available |
| full_grid | mcar | galpi | 0.10 | 0.50 | 5.1421 | 9.9201 | 4.7780 | 92.92 | 0.000 | available |
| full_grid | mcar | linear_interpolation | 0.10 | 0.50 | 5.1113 | 7.6956 | 2.5843 | 50.56 | 0.617 | available |
| full_grid | mcar | nearest_interpolation | 0.10 | 0.50 | 8.2913 | 10.2994 | 2.0080 | 24.22 | 1.000 | available |
| full_grid | mnar | cubic_spline | 0.10 | 0.50 | 6.8779 | 9.1859 | 2.3081 | 33.56 | 0.768 | available |
| full_grid | mnar | galpi | 0.10 | 0.50 | 7.2226 | 13.6704 | 6.4479 | 89.27 | 0.000 | available |
| full_grid | mnar | linear_interpolation | 0.10 | 0.50 | 7.1843 | 10.2320 | 3.0476 | 42.42 | 0.646 | available |
| full_grid | mnar | nearest_interpolation | 0.10 | 0.50 | 12.1923 | 14.2285 | 2.0362 | 16.70 | 1.000 | available |
