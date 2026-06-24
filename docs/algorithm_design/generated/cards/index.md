# Algorithm Scientific Cards

One scientific card per canonical algorithm in the ImputeBench workbench. Each card describes the algorithm principle, implementation, configuration surface, evidence status, and limitations.

_Generated at: 2026-05-09T10:25:35.724528+00:00_

## Card index

### Baselines

- [LinearInterpolation](baselines/LinearInterpolation.md) — ✅ Execution ready
- [LOCF](baselines/LOCF.md) — ✅ Execution ready
- [Mean](baselines/Mean.md) — ✅ Execution ready
- [Median](baselines/Median.md) — ✅ Execution ready

### Classical Methods

- [BackwardFill](classical/BackwardFill.md) — ✅ Execution ready
- [ExponentialSmoothing](classical/ExponentialSmoothing.md) — ✅ Execution ready
- [MovingAverage](classical/MovingAverage.md) — ✅ Execution ready
- [NearestInterpolation](classical/NearestInterpolation.md) — ✅ Execution ready
- [SeasonalNaive](classical/SeasonalNaive.md) — ✅ Execution ready

### Deep Learning / Temporal Methods

- [BRITS](dl_temporal/BRITS.md) — ✅ Execution ready
- [Simple GRU](dl_temporal/Simple_GRU.md) — ✅ Execution ready
- [GRU-D](dl_temporal/GRU_D.md) — ✅ Execution ready
- [Simple LSTM](dl_temporal/Simple_LSTM.md) — ✅ Execution ready
- [Simple RNN](dl_temporal/Simple_RNN.md) — ✅ Execution ready
- [SAITS](dl_temporal/SAITS.md) — ✅ Execution ready
- [SAITS-LC](dl_temporal/SAITS_LC.md) — ✅ Execution ready
- [SAITS-LCH](dl_temporal/SAITS_LCH.md) — ✅ Execution ready

## Status legend

All statuses are RESOLVED through the unified audit pipeline. AD2 (execution truth) is authoritative: runtime evidence always overrides static AD0 inventory values.

| Status | Meaning |
| --- | --- |
| ✅ Execution ready | Runtime execution confirmed via SQLite queries |
| 🟡 Execution partial | Some runtime evidence but incomplete |
| ❌ Execution invalid | Runtime execution failed or produced invalid output |
| ⚪ Not executed | No runtime evidence found in database |
| 🟢 Implementation ready | Code is callable and tested, not yet executed |
| 🟠 Partial | Partial evidence only |
| 🔴 Missing | Implementation missing or unresolved |
