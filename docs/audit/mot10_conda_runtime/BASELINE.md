# MOT10 Conda Runtime Baseline

Date: 2026-06-25

## CLI Root

Before MOT10, the root CLI exposed progress/deprecation options only:

- `--progress / --no-progress`
- `--progress-backend`
- `--progress-event-log`
- `--no-deprecation-warnings`

It did not expose root CUDA/runtime environment controls.

## CUDA Diagnostics

`ExecutionPolicyService` was importable without Torch, but CUDA diagnostics were
computed through repeated `_cuda_available()` calls from:

- `cuda_available()`
- `effective_compute_device()`
- `effective_device_label()`
- `runtime_truth_snapshot()`
- `placement_evidence_snapshot()`

CPU-only PyTorch could therefore log the same CUDA warning repeatedly.

## Conda Runtime

Conda was not part of the CLI root policy. `lab start` had a legacy helper that
looked for a `deeplearning` Python executable, but experiment commands did not
perform a controlled `conda run` relaunch.

## Current Local Probe

The local `base` environment uses CPU-only PyTorch:

```powershell
python -m imputebench --no-env-relaunch --cuda off admin env cuda --format json
```

The target `deeplearning` environment probes successfully via `conda run` and
reports CUDA availability on this workstation.

