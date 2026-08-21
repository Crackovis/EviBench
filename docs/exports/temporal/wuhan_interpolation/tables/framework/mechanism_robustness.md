# Mechanism robustness (MCAR / MAR / MNAR sensitivity)

Requires all of MCAR, MAR and MNAR for a station × rate cell; robustness = 1 − relative range of RMSE across mechanisms.

| Station | Rate | Algorithm | Mechanisms | Min RMSE | Max RMSE | Relative range | Score | Status |
|---|---|---|---|---|---|---|---|---|
| full_grid | 0.10 | cubic_spline | 3 | 5.1102 | 6.8779 | 0.2570 | 0.743 | available |
| full_grid | 0.10 | galpi | 3 | 5.1421 | 7.2226 | 0.2880 | 0.712 | available |
| full_grid | 0.10 | linear_interpolation | 3 | 5.1113 | 7.1843 | 0.2885 | 0.711 | available |
| full_grid | 0.10 | nearest_interpolation | 3 | 8.2913 | 12.1923 | 0.3200 | 0.680 | available |
| full_grid | 0.30 | cubic_spline | 3 | 6.2474 | 7.7384 | 0.1927 | 0.807 | available |
| full_grid | 0.30 | galpi | 3 | 6.7241 | 8.9300 | 0.2470 | 0.753 | available |
| full_grid | 0.30 | linear_interpolation | 3 | 6.0708 | 8.1766 | 0.2575 | 0.742 | available |
| full_grid | 0.30 | nearest_interpolation | 3 | 8.8899 | 12.7449 | 0.3025 | 0.698 | available |
| full_grid | 0.50 | cubic_spline | 3 | 8.1574 | 19.3106 | 0.5776 | 0.422 | available |
| full_grid | 0.50 | galpi | 3 | 9.9201 | 16.7447 | 0.4076 | 0.592 | available |
| full_grid | 0.50 | linear_interpolation | 3 | 7.6956 | 13.2249 | 0.4181 | 0.582 | available |
| full_grid | 0.50 | nearest_interpolation | 3 | 10.2994 | 15.3717 | 0.3300 | 0.670 | available |
