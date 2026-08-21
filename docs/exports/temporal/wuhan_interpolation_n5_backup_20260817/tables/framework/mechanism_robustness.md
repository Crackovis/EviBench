# Mechanism robustness (MCAR / MAR / MNAR sensitivity)

Requires all of MCAR, MAR and MNAR for a station × rate cell; robustness = 1 − relative range of RMSE across mechanisms.

| Station | Rate | Algorithm | Mechanisms | Min RMSE | Max RMSE | Relative range | Score | Status |
|---|---|---|---|---|---|---|---|---|
| full_grid | 0.10 | cubic_spline | 3 | 5.1084 | 6.8065 | 0.2495 | 0.751 | available |
| full_grid | 0.10 | galpi | 3 | 5.1422 | 7.1017 | 0.2759 | 0.724 | available |
| full_grid | 0.10 | linear_interpolation | 3 | 5.1080 | 7.0710 | 0.2776 | 0.722 | available |
| full_grid | 0.10 | nearest_interpolation | 3 | 8.2925 | 12.1189 | 0.3157 | 0.684 | available |
| full_grid | 0.30 | cubic_spline | 3 | 6.2541 | 7.6809 | 0.1858 | 0.814 | available |
| full_grid | 0.30 | galpi | 3 | 6.7225 | 8.8985 | 0.2445 | 0.755 | available |
| full_grid | 0.30 | linear_interpolation | 3 | 6.0715 | 8.1237 | 0.2526 | 0.747 | available |
| full_grid | 0.30 | nearest_interpolation | 3 | 8.8943 | 12.7267 | 0.3011 | 0.699 | available |
| full_grid | 0.50 | cubic_spline | 3 | 8.1425 | 19.5414 | 0.5833 | 0.417 | available |
| full_grid | 0.50 | galpi | 3 | 9.9044 | 16.7084 | 0.4072 | 0.593 | available |
| full_grid | 0.50 | linear_interpolation | 3 | 7.6931 | 13.3105 | 0.4220 | 0.578 | available |
| full_grid | 0.50 | nearest_interpolation | 3 | 10.2899 | 15.4781 | 0.3352 | 0.665 | available |
