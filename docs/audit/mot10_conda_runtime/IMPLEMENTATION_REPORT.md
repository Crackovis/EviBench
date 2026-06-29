# MOT10 Conda Runtime Implementation Report

Date: 2026-06-25

## Implemented

- Added root CLI options: `--cuda auto|require|off|env:<name>`, `--runtime-env`, and `--no-env-relaunch`.
- Added command classification before subcommand execution.
- Added optional Conda detection and CUDA probing via `conda info --envs --json` and `conda run -n <env> python -c ...`.
- Added one-shot relaunch through `conda run -n <env> python -m imputebench.cli_runner`.
- Added relaunch guard variables: `IMPUTEBENCH_ENV_RELAUNCHED`, source/target env, CUDA policy, and command GPU class.
- Kept `results export-human` classified as `not_applicable`.
- Added CUDA warning-once and process-level CUDA diagnostic cache in `ExecutionPolicyService`.
- Added `runtime_environment_payload` to DL/ST result payloads without a SQL migration.
- Added canonical diagnostics under `imputebench admin env show|cuda|conda` while preserving `admin env`.

## Validation

```powershell
pytest tests/runtime_environment -q
pytest tests/test_obj7_gpu_truth_contract.py -q -k "not TrainingLoopServiceDeviceAuthority"
python -m compileall -q imputebench
python -m imputebench --help
python -m imputebench --no-env-relaunch --cuda off admin env cuda --format json
python -m imputebench --no-env-relaunch --cuda require experiment temporal experiment run --execution-class dl --dry-run
python -m imputebench --cuda require experiment temporal experiment run --execution-class dl --dry-run
python -m imputebench --cuda require results export-human --dry-run --output-dir tmp/export_test --recipe-book official_londonaq_classical_benchmark --tier a
```

Observed local behavior:

- `base` is CPU-only (`torch 2.10.0+cpu`).
- `deeplearning` probes as CUDA-capable (`torch 2.7.1+cu118`, CUDA 11.8, NVIDIA GeForce RTX 3050 Ti Laptop GPU).
- `--cuda require --no-env-relaunch` fails before executing the DL command.
- `--cuda require` relaunches and preserves the child command output.
- `results export-human` does not trigger Conda relaunch.

## Known Residual

`pytest tests/test_obj7_gpu_truth_contract.py -q` still has 4 pre-existing
failures in `TestTrainingLoopServiceDeviceAuthority`, all inside
`TrainingLoopService.fit` / real Torch stub interaction. The MOT10-related GPU
truth contract subset passes when that class is deselected.

