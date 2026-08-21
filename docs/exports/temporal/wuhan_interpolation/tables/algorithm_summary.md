# Per-recipe algorithm summary

### full_grid / MAR 10%

*Table 0.2: Per-algorithm metrics for recipe mar_10.*

Station: `full_grid` · Recipe: MAR 10%

| Algorithm | Family | N | Mean RMSE | Std RMSE | Median RMSE | Mean MAE | Mean runtime | Rank | Evidence status |
|---|---|---|---|---|---|---|---|---|---|
| cubic_spline | classical | 20 | **5.3298** | 0.2304 | 5.4229 | 2.6981 | 5.7129 | 1 | compatible |
| linear_interpolation | naive | 20 | 5.3422 | 0.2444 | 5.4417 | 2.7093 | 0.1600 | 2 | compatible |
| galpi | classical | 20 | 5.4280 | 0.2457 | 5.5195 | 2.7340 | 5.2228 | 3 | compatible |
| nearest_interpolation | classical | 20 | 8.4709 | 0.2827 | 8.5332 | 4.3362 | 0.1996 | 4 | compatible |

### full_grid / MAR 30%

*Table 0.3: Per-algorithm metrics for recipe mar_30.*

Station: `full_grid` · Recipe: MAR 30%

| Algorithm | Family | N | Mean RMSE | Std RMSE | Median RMSE | Mean MAE | Mean runtime | Rank | Evidence status |
|---|---|---|---|---|---|---|---|---|---|
| linear_interpolation | naive | 20 | **7.0844** | 0.3251 | 7.1297 | 3.5122 | 0.2185 | 1 | compatible |
| cubic_spline | classical | 20 | 7.3327 | 0.3439 | 7.3731 | 3.6233 | 9.7017 | 2 | compatible |
| galpi | classical | 20 | 8.8128 | 0.3286 | 8.8596 | 4.0779 | 11.4269 | 3 | compatible |
| nearest_interpolation | classical | 20 | 9.7946 | 0.3088 | 9.8137 | 4.8759 | 0.3423 | 4 | compatible |

### full_grid / MAR 50%

*Table 0.4: Per-algorithm metrics for recipe mar_50.*

Station: `full_grid` · Recipe: MAR 50%

| Algorithm | Family | N | Mean RMSE | Std RMSE | Median RMSE | Mean MAE | Mean runtime | Rank | Evidence status |
|---|---|---|---|---|---|---|---|---|---|
| linear_interpolation | naive | 20 | **13.2249** | 0.4309 | 13.2996 | 6.1269 | 0.4066 | 1 | compatible |
| nearest_interpolation | classical | 20 | 15.3717 | 0.4104 | 15.4445 | 7.2330 | 0.5102 | 2 | compatible |
| galpi | classical | 20 | 16.7447 | 0.3687 | 16.6508 | 7.8147 | 30.6397 | 3 | compatible |
| cubic_spline | classical | 20 | 19.3106 | 3.1428 | 20.6601 | 7.5987 | 26.4997 | 4 | compatible |

### full_grid / MCAR 10%

*Table 0.5: Per-algorithm metrics for recipe mcar_10.*

Station: `full_grid` · Recipe: MCAR 10%

| Algorithm | Family | N | Mean RMSE | Std RMSE | Median RMSE | Mean MAE | Mean runtime | Rank | Evidence status |
|---|---|---|---|---|---|---|---|---|---|
| cubic_spline | classical | 20 | **5.1102** | 0.0319 | 5.1083 | 2.5846 | 6.4845 | 1 | compatible |
| linear_interpolation | naive | 20 | 5.1113 | 0.0317 | 5.1097 | 2.5961 | 0.1948 | 2 | compatible |
| galpi | classical | 20 | 5.1421 | 0.0300 | 5.1379 | 2.6050 | 7.0250 | 3 | compatible |
| nearest_interpolation | classical | 20 | 8.2913 | 0.0386 | 8.2896 | 4.2325 | 0.2716 | 4 | compatible |

### full_grid / MCAR 30%

*Table 0.6: Per-algorithm metrics for recipe mcar_30.*

Station: `full_grid` · Recipe: MCAR 30%

| Algorithm | Family | N | Mean RMSE | Std RMSE | Median RMSE | Mean MAE | Mean runtime | Rank | Evidence status |
|---|---|---|---|---|---|---|---|---|---|
| linear_interpolation | naive | 20 | **6.0708** | 0.0240 | 6.0706 | 3.0523 | 0.2382 | 1 | compatible |
| cubic_spline | classical | 20 | 6.2474 | 0.0270 | 6.2378 | 3.1292 | 14.1314 | 2 | compatible |
| galpi | classical | 20 | 6.7241 | 0.0405 | 6.7202 | 3.2568 | 15.3515 | 3 | compatible |
| nearest_interpolation | classical | 20 | 8.8899 | 0.0241 | 8.8905 | 4.4677 | 0.4131 | 4 | compatible |

### full_grid / MCAR 50%

*Table 0.7: Per-algorithm metrics for recipe mcar_50.*

Station: `full_grid` · Recipe: MCAR 50%

| Algorithm | Family | N | Mean RMSE | Std RMSE | Median RMSE | Mean MAE | Mean runtime | Rank | Evidence status |
|---|---|---|---|---|---|---|---|---|---|
| linear_interpolation | naive | 20 | **7.6956** | 0.0369 | 7.6951 | 3.8075 | 0.4653 | 1 | compatible |
| cubic_spline | classical | 20 | 8.1574 | 0.0731 | 8.1400 | 4.0081 | 21.3288 | 2 | compatible |
| galpi | classical | 20 | 9.9201 | 0.0521 | 9.9180 | 4.6256 | 27.2966 | 3 | compatible |
| nearest_interpolation | classical | 20 | 10.2994 | 0.0363 | 10.3050 | 5.0484 | 0.6141 | 4 | compatible |

### full_grid / MNAR 10%

*Table 0.8: Per-algorithm metrics for recipe mnar_10.*

Station: `full_grid` · Recipe: MNAR 10%

| Algorithm | Family | N | Mean RMSE | Std RMSE | Median RMSE | Mean MAE | Mean runtime | Rank | Evidence status |
|---|---|---|---|---|---|---|---|---|---|
| cubic_spline | classical | 20 | **6.8779** | 0.1381 | 6.8826 | 3.6927 | 1.1065 | 1 | compatible |
| linear_interpolation | naive | 20 | 7.1843 | 0.1506 | 7.1662 | 3.8866 | 0.1229 | 2 | compatible |
| galpi | classical | 20 | 7.2226 | 0.1459 | 7.2462 | 3.8925 | 2.2555 | 3 | compatible |
| nearest_interpolation | classical | 20 | 12.1923 | 0.1328 | 12.1827 | 6.9852 | 0.2026 | 4 | compatible |

### full_grid / MNAR 30%

*Table 0.9: Per-algorithm metrics for recipe mnar_30.*

Station: `full_grid` · Recipe: MNAR 30%

| Algorithm | Family | N | Mean RMSE | Std RMSE | Median RMSE | Mean MAE | Mean runtime | Rank | Evidence status |
|---|---|---|---|---|---|---|---|---|---|
| cubic_spline | classical | 20 | **7.7384** | 0.0850 | 7.7146 | 4.0545 | 3.8214 | 1 | compatible |
| linear_interpolation | naive | 20 | 8.1766 | 0.0835 | 8.1854 | 4.2891 | 0.1288 | 2 | compatible |
| galpi | classical | 20 | 8.9300 | 0.1318 | 8.9150 | 4.4346 | 3.1893 | 3 | compatible |
| nearest_interpolation | classical | 20 | 12.7449 | 0.0668 | 12.7481 | 7.1693 | 0.2203 | 4 | compatible |

### full_grid / MNAR 50%

*Table 0.10: Per-algorithm metrics for recipe mnar_50.*

Station: `full_grid` · Recipe: MNAR 50%

| Algorithm | Family | N | Mean RMSE | Std RMSE | Median RMSE | Mean MAE | Mean runtime | Rank | Evidence status |
|---|---|---|---|---|---|---|---|---|---|
| cubic_spline | classical | 20 | **9.1859** | 0.1017 | 9.1867 | 4.5678 | 5.0182 | 1 | compatible |
| linear_interpolation | naive | 20 | 10.2320 | 0.0943 | 10.2354 | 4.9560 | 0.1829 | 2 | compatible |
| galpi | classical | 20 | 13.6704 | 0.2102 | 13.6372 | 5.6762 | 5.5787 | 3 | compatible |
| nearest_interpolation | classical | 20 | 14.2285 | 0.0920 | 14.2152 | 7.6096 | 0.2581 | 4 | compatible |
