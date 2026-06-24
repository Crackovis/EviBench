# MOT04 Baseline

This audit directory tracks the parallel human evidence export implementation.

The pre-patch wall-clock baseline cannot be regenerated from the current
working tree after implementation. The implementation therefore records the
local environment and provides a repeatable benchmark script so the baseline and
post-patch runs can be produced on the same machine with the same command.

Recommended baseline command from the MOT04 spec:

```bash
python scripts/benchmark_human_export_parallel.py \
  --recipe-book official_londonaq_classical_benchmark \
  --tier a \
  --algorithm-id linear_interpolation \
  --max-targets 400 \
  --workers 1
```

Local environment captured during implementation:

- HEAD: `79d3e367261bfb7ee801515e288442eb44e15eab`
- OS: Windows-11-10.0.26200-SP0
- Python: 3.13.9
- CPU count: 20
