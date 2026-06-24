# Algorithm Design Register

Supervisor-readable overview of every canonical algorithm, its family, role, implementation kind, and **resolved** evidence status (authoritative: AD2 execution truth > AD1.5 conformance > AD0 inventory).

_Generated at: 2026-05-09T10:25:35.724528+00:00_

## How to read this register

Each row represents one of the 17 canonical algorithms. The columns tell a story:

- **AD0 status**: What the static inventory found by scanning plugin manifests and source files. May say "partial" because it cannot verify runtime execution.
- **AD2 verdict**: What actual runtime evidence from the SQLite database proves. This is the AUTHORITATIVE column — it queries real execution results.
- **Resolved status**: The final answer. When AD0 and AD2 disagree, AD2 always wins. Runtime evidence trumps static analysis.

### Why some algorithms show "partial" in AD0 but "execution_ready" resolved

The legacy AD0 inventory only does static checks (source file scanning, plugin manifest reading). It cannot know whether an algorithm was actually executed. AD2 queries the benchmark database directly and sees the real results — 45 results per algorithm, with checkpoints, training histories, and prediction fingerprints. That's why the resolved status shows execution_ready even when AD0 still says partial.

## Register

| Algorithm | Family | Role | AD0 status | AD2 verdict | AD1.5 verdict | **Resolved status** | Resolution note |
| --- | --- | --- | --- | --- | --- | --- | --- |
| BRITS | dl_temporal | deep_learning_reference | partial | execution_ready | mostly_conformant | **execution_ready** | AD2 execution truth overrides AD0 partial |
| Simple GRU | dl_temporal | deep_learning_reference | partial | execution_ready | partial_conformance | **execution_ready** | AD2 execution truth overrides AD0 partial |
| GRU-D | dl_temporal | deep_learning_reference | partial | execution_ready | mostly_conformant | **execution_ready** | AD2 execution truth overrides AD0 partial |
| Simple LSTM | dl_temporal | deep_learning_reference | partial | execution_ready | partial_conformance | **execution_ready** | AD2 execution truth overrides AD0 partial |
| Simple RNN | dl_temporal | deep_learning_reference | partial | execution_ready | partial_conformance | **execution_ready** | AD2 execution truth overrides AD0 partial |
| SAITS | dl_temporal | deep_learning_reference | partial | execution_ready | mostly_conformant | **execution_ready** | AD2 execution truth overrides AD0 partial |
| SAITS-LC | dl_temporal | deep_learning_reference | partial | execution_ready | mostly_conformant | **execution_ready** | AD2 execution truth overrides AD0 partial |
| SAITS-LCH | dl_temporal | deep_learning_reference | partial | execution_ready | mostly_conformant | **execution_ready** | AD2 execution truth overrides AD0 partial |
| BackwardFill | classical | classical_reference | partial | execution_ready | mostly_conformant | **execution_ready** | AD2 execution truth overrides AD0 partial |
| ExponentialSmoothing | classical | classical_reference | partial | execution_ready | mostly_conformant | **execution_ready** | AD2 execution truth overrides AD0 partial |
| MovingAverage | classical | classical_reference | partial | execution_ready | conformant | **execution_ready** | AD2 execution truth overrides AD0 partial |
| NearestInterpolation | classical | classical_reference | partial | execution_ready | mostly_conformant | **execution_ready** | AD2 execution truth overrides AD0 partial |
| SeasonalNaive | classical | classical_reference | partial | execution_ready | mostly_conformant | **execution_ready** | AD2 execution truth overrides AD0 partial |
| LinearInterpolation | naive | baseline | partial | execution_ready | conformant | **execution_ready** | AD2 execution truth overrides AD0 partial |
| LOCF | naive | baseline | partial | execution_ready | mostly_conformant | **execution_ready** | AD2 execution truth overrides AD0 partial |
| Mean | naive | baseline | partial | execution_ready | conformant | **execution_ready** | AD2 execution truth overrides AD0 partial |
| Median | naive | baseline | partial | execution_ready | conformant | **execution_ready** | AD2 execution truth overrides AD0 partial |

---

## Status summary (resolved)

- **execution_ready:** 17
- **execution_partial:** 0
- **execution_invalid:** 0
- **not_executed:** 0
- **not_runnable:** 0
- **implementation_ready:** 0
- **partial:** 0
- **missing:** 0

> **Note:** This register uses **resolved** statuses from the unified audit pipeline. AD2 (execution truth) is authoritative and always overrides AD0 inventory values. No more 'partial' when runtime says 'execution_ready'.
