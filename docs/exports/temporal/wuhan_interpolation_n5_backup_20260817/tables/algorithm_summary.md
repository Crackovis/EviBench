# Per-recipe algorithm summary

### full_grid / MAR 10%

*Table 0.2: Per-algorithm metrics for recipe mar_10.*

Station: `full_grid` · Recipe: MAR 10%

| Algorithm | Family | N | Mean RMSE | Std RMSE | Median RMSE | Mean MAE | Mean runtime | Rank | Evidence status |
|---|---|---|---|---|---|---|---|---|---|
| cubic_spline | classical | 5 | **5.3982** | 0.2239 | 5.4551 | 2.7307 | 3.0621 | 1 | compatible |
| linear_interpolation | naive | 5 | 5.3989 | 0.2364 | 5.4798 | 2.7380 | 0.1288 | 2 | compatible |
| galpi | classical | 5 | 5.4847 | 0.2370 | 5.5674 | 2.7636 | 3.6540 | 3 | compatible |
| nearest_interpolation | classical | 5 | 8.5242 | 0.2313 | 8.5792 | 4.3721 | 0.2302 | 4 | compatible |

### full_grid / MAR 30%

*Table 0.3: Per-algorithm metrics for recipe mar_30.*

Station: `full_grid` · Recipe: MAR 30%

| Algorithm | Family | N | Mean RMSE | Std RMSE | Median RMSE | Mean MAE | Mean runtime | Rank | Evidence status |
|---|---|---|---|---|---|---|---|---|---|
| linear_interpolation | naive | 5 | **7.1430** | 0.3182 | 7.3210 | 3.5433 | 0.1956 | 1 | compatible |
| cubic_spline | classical | 5 | 7.4363 | 0.3525 | 7.5471 | 3.6624 | 7.8682 | 2 | compatible |
| galpi | classical | 5 | 8.8595 | 0.3341 | 9.0499 | 4.1121 | 8.8974 | 3 | compatible |
| nearest_interpolation | classical | 5 | 9.8552 | 0.2905 | 10.0054 | 4.9139 | 0.2715 | 4 | compatible |

### full_grid / MAR 50%

*Table 0.4: Per-algorithm metrics for recipe mar_50.*

Station: `full_grid` · Recipe: MAR 50%

| Algorithm | Family | N | Mean RMSE | Std RMSE | Median RMSE | Mean MAE | Mean runtime | Rank | Evidence status |
|---|---|---|---|---|---|---|---|---|---|
| linear_interpolation | naive | 5 | **13.3105** | 0.4410 | 13.5772 | 6.1795 | 0.2898 | 1 | compatible |
| nearest_interpolation | classical | 5 | 15.4781 | 0.4316 | 15.7129 | 7.2910 | 0.3966 | 2 | compatible |
| galpi | classical | 5 | 16.7084 | 0.1355 | 16.6386 | 7.8505 | 13.2833 | 3 | compatible |
| cubic_spline | classical | 5 | 19.5414 | 2.6500 | 20.2344 | 7.7194 | 11.5818 | 4 | compatible |

### full_grid / MCAR 10%

*Table 0.5: Per-algorithm metrics for recipe mcar_10.*

Station: `full_grid` · Recipe: MCAR 10%

| Algorithm | Family | N | Mean RMSE | Std RMSE | Median RMSE | Mean MAE | Mean runtime | Rank | Evidence status |
|---|---|---|---|---|---|---|---|---|---|
| linear_interpolation | naive | 5 | **5.1080** | 0.0297 | 5.1201 | 2.5943 | 0.1477 | 1 | compatible |
| cubic_spline | classical | 5 | 5.1084 | 0.0331 | 5.1120 | 2.5825 | 3.2877 | 2 | compatible |
| galpi | classical | 5 | 5.1422 | 0.0262 | 5.1525 | 2.6033 | 3.9948 | 3 | compatible |
| nearest_interpolation | classical | 5 | 8.2925 | 0.0420 | 8.3001 | 4.2316 | 0.2148 | 4 | compatible |

### full_grid / MCAR 30%

*Table 0.6: Per-algorithm metrics for recipe mcar_30.*

Station: `full_grid` · Recipe: MCAR 30%

| Algorithm | Family | N | Mean RMSE | Std RMSE | Median RMSE | Mean MAE | Mean runtime | Rank | Evidence status |
|---|---|---|---|---|---|---|---|---|---|
| linear_interpolation | naive | 5 | **6.0715** | 0.0312 | 6.0706 | 3.0517 | 0.2100 | 1 | compatible |
| cubic_spline | classical | 5 | 6.2541 | 0.0382 | 6.2365 | 3.1299 | 8.5357 | 2 | compatible |
| galpi | classical | 5 | 6.7225 | 0.0530 | 6.7516 | 3.2576 | 8.6908 | 3 | compatible |
| nearest_interpolation | classical | 5 | 8.8943 | 0.0244 | 8.8951 | 4.4690 | 0.3001 | 4 | compatible |

### full_grid / MCAR 50%

*Table 0.7: Per-algorithm metrics for recipe mcar_50.*

Station: `full_grid` · Recipe: MCAR 50%

| Algorithm | Family | N | Mean RMSE | Std RMSE | Median RMSE | Mean MAE | Mean runtime | Rank | Evidence status |
|---|---|---|---|---|---|---|---|---|---|
| linear_interpolation | naive | 5 | **7.6931** | 0.0209 | 7.6822 | 3.8068 | 0.3096 | 1 | compatible |
| cubic_spline | classical | 5 | 8.1425 | 0.0450 | 8.1440 | 4.0079 | 12.6047 | 2 | compatible |
| galpi | classical | 5 | 9.9044 | 0.0309 | 9.9177 | 4.6228 | 15.0364 | 3 | compatible |
| nearest_interpolation | classical | 5 | 10.2899 | 0.0215 | 10.2899 | 5.0474 | 0.4223 | 4 | compatible |

### full_grid / MNAR 10%

*Table 0.8: Per-algorithm metrics for recipe mnar_10.*

Station: `full_grid` · Recipe: MNAR 10%

| Algorithm | Family | N | Mean RMSE | Std RMSE | Median RMSE | Mean MAE | Mean runtime | Rank | Evidence status |
|---|---|---|---|---|---|---|---|---|---|
| cubic_spline | classical | 5 | **6.8065** | 0.0824 | 6.7764 | 3.6948 | 1.0860 | 1 | compatible |
| linear_interpolation | naive | 5 | 7.0710 | 0.1072 | 7.0203 | 3.8747 | 0.0868 | 2 | compatible |
| galpi | classical | 5 | 7.1017 | 0.0940 | 7.0480 | 3.8811 | 1.5952 | 3 | compatible |
| nearest_interpolation | classical | 5 | 12.1189 | 0.1277 | 12.1321 | 6.9707 | 0.1536 | 4 | compatible |

### full_grid / MNAR 30%

*Table 0.9: Per-algorithm metrics for recipe mnar_30.*

Station: `full_grid` · Recipe: MNAR 30%

| Algorithm | Family | N | Mean RMSE | Std RMSE | Median RMSE | Mean MAE | Mean runtime | Rank | Evidence status |
|---|---|---|---|---|---|---|---|---|---|
| cubic_spline | classical | 5 | **7.6809** | 0.0590 | 7.7005 | 4.0545 | 2.1640 | 1 | compatible |
| linear_interpolation | naive | 5 | 8.1237 | 0.1143 | 8.1746 | 4.2863 | 0.1187 | 2 | compatible |
| galpi | classical | 5 | 8.8985 | 0.1870 | 8.9010 | 4.4344 | 2.7809 | 3 | compatible |
| nearest_interpolation | classical | 5 | 12.7267 | 0.1047 | 12.7750 | 7.1659 | 0.1702 | 4 | compatible |

### full_grid / MNAR 50%

*Table 0.10: Per-algorithm metrics for recipe mnar_50.*

Station: `full_grid` · Recipe: MNAR 50%

| Algorithm | Family | N | Mean RMSE | Std RMSE | Median RMSE | Mean MAE | Mean runtime | Rank | Evidence status |
|---|---|---|---|---|---|---|---|---|---|
| cubic_spline | classical | 5 | **9.1625** | 0.0807 | 9.1514 | 4.5627 | 3.0605 | 1 | compatible |
| linear_interpolation | naive | 5 | 10.2114 | 0.0885 | 10.2252 | 4.9445 | 0.1386 | 2 | compatible |
| galpi | classical | 5 | 13.6703 | 0.1602 | 13.6388 | 5.6572 | 3.6015 | 3 | compatible |
| nearest_interpolation | classical | 5 | 14.2335 | 0.0918 | 14.2318 | 7.6009 | 0.2054 | 4 | compatible |
