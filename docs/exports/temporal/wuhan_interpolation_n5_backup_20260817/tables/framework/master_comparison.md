# Master multi-dimensional comparison

Five relative dimension scores (each in [0,1], higher is better within the balanced cohort intersection). Parameter and memory are optional and blank when no evidence exists. **There is no weighted overall score.**

| Algorithm | Accuracy | Speed | Stability | Rate robustness | Mechanism robustness | Parameter | Memory | Cohort coverage | Dimension coverage | Profile status |
|---|---|---|---|---|---|---|---|---|---|---|
| Cubic Spline | 0.850 | 0.056 | 0.300 | 0.419 | 0.660 | — | — | 1.00 | 1.00 | available |
| Galpi | 0.613 | 0.000 | 0.407 | 0.106 | 0.691 | — | — | 1.00 | 1.00 | available |
| Linear Interpolation | 0.962 | 1.000 | 0.432 | 0.631 | 0.683 | — | — | 1.00 | 1.00 | available |
| Nearest Interpolation | 0.072 | 0.898 | 0.978 | 1.000 | 0.683 | — | — | 1.00 | 1.00 | available |
