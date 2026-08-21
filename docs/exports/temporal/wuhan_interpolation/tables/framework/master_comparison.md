# Master multi-dimensional comparison

Five relative dimension scores (each in [0,1], higher is better within the balanced cohort intersection). Parameter and memory are optional and blank when no evidence exists. **There is no weighted overall score.**

| Algorithm | Accuracy | Speed | Stability | Rate robustness | Mechanism robustness | Parameter | Memory | Cohort coverage | Dimension coverage | Profile status |
|---|---|---|---|---|---|---|---|---|---|---|
| Cubic Spline | 0.852 | 0.061 | 0.187 | 0.417 | 0.658 | — | — | 1.00 | 1.00 | available |
| Galpi | 0.607 | 0.013 | 0.297 | 0.099 | 0.686 | — | — | 1.00 | 1.00 | available |
| Linear Interpolation | 0.960 | 1.000 | 0.397 | 0.632 | 0.679 | — | — | 1.00 | 1.00 | available |
| Nearest Interpolation | 0.072 | 0.906 | 0.996 | 1.000 | 0.683 | — | — | 1.00 | 1.00 | available |
