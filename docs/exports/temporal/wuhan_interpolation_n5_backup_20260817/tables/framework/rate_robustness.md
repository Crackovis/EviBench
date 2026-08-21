# Rate robustness (degradation between missingness rates)

Descriptive degradation between strictly comparable cells (same dataset view, station, algorithm, mechanism, phase, metric, recipe lineage). The low and high supports differ, so this dimension stays descriptive.

| Station | Mechanism | Algorithm | Low rate | High rate | RMSE low | RMSE high | Δ abs | Δ rel % | Score | Status |
|---|---|---|---|---|---|---|---|---|---|---|
| full_grid | mar | cubic_spline | 0.10 | 0.50 | 5.3982 | 19.5414 | 14.1432 | 262.00 | 0.000 | available |
| full_grid | mar | galpi | 0.10 | 0.50 | 5.4847 | 16.7084 | 11.2236 | 204.63 | 0.318 | available |
| full_grid | mar | linear_interpolation | 0.10 | 0.50 | 5.3989 | 13.3105 | 7.9117 | 146.54 | 0.640 | available |
| full_grid | mar | nearest_interpolation | 0.10 | 0.50 | 8.5242 | 15.4781 | 6.9539 | 81.58 | 1.000 | available |
| full_grid | mcar | cubic_spline | 0.10 | 0.50 | 5.1084 | 8.1425 | 3.0341 | 59.39 | 0.485 | available |
| full_grid | mcar | galpi | 0.10 | 0.50 | 5.1422 | 9.9044 | 4.7622 | 92.61 | 0.000 | available |
| full_grid | mcar | linear_interpolation | 0.10 | 0.50 | 5.1080 | 7.6931 | 2.5850 | 50.61 | 0.613 | available |
| full_grid | mcar | nearest_interpolation | 0.10 | 0.50 | 8.2925 | 10.2899 | 1.9974 | 24.09 | 1.000 | available |
| full_grid | mnar | cubic_spline | 0.10 | 0.50 | 6.8065 | 9.1625 | 2.3559 | 34.61 | 0.771 | available |
| full_grid | mnar | galpi | 0.10 | 0.50 | 7.1017 | 13.6703 | 6.5686 | 92.49 | 0.000 | available |
| full_grid | mnar | linear_interpolation | 0.10 | 0.50 | 7.0710 | 10.2114 | 3.1404 | 44.41 | 0.641 | available |
| full_grid | mnar | nearest_interpolation | 0.10 | 0.50 | 12.1189 | 14.2335 | 2.1147 | 17.45 | 1.000 | available |
