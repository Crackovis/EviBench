# Sheet 12 — Phase 12-00 Baseline Freeze

Audited ref: `0f543fc0` (HEAD `7c5751f8` adds only spec files; code is identical).

## Service composition at audited HEAD (temporal CLI)

`build_temporal_services().preparation._automation` is an
`OfficialPreparationAutomationService` constructed with:

| dependency | state |
| --- | --- |
| `_datasets` (DatasetService) | **None** |
| `_board_svc` (board) | **None** |
| `_action_svc` (actions) | **None** |
| `_guided` (guided plans) | **None** |
| `_catalog` (artifact catalog) | **None** |
| `_banks` | BenchmarkMaskBankService |
| `_contracts` | BenchmarkContractService |
| `_helpers` | `_BuiltinRecipeHelper` (only `get_recipe_book`) |

`OfficialPreparationAutomationService._load_dataset_array('london_aq')` → `None`
(because `self._datasets is None`).

## Failing materialize (clean benchmark-asset state)

Live DB already has **0 benchmark contracts** and **0 mask banks**.

```
imputebench experiment temporal prepare materialize \
  --recipe-book official_londonaq_classical_benchmark --tier a --dataset-id london_aq
```

Result:
```
status: blocked
mask_banks_created: 0
contracts_created: 0
errors:
  - Could not load dataset 'london_aq' for mask generation.
  - Could not build preparation board.
  - Could not build preparation board.
  - Could not build preparation board.
```

(1 root dataset-load failure + 3 cascading board failures.) No mutation occurred.

## Successful dispatcher-equivalent dataset load

`DatasetService.load_array('london_aq')` raises `KeyError: Dataset not found`
(the alias is not directly resolvable). The canonical authority resolves it:

```
DatasetRuntimeProvider(DatasetService()).load('london_aq')
  -> resolved id fa9c7108-176e-4c68-840d-6acf3a3e59e4
  -> x_true ndarray (600, 10, 14, 4) float64
```

The registered dataset is `London AQ Synthetic`
(id `fa9c7108-176e-4c68-840d-6acf3a3e59e4`, tags `['london','thesis-v0.2','phase1']`,
pollutants `['NO','NO2','O3','SO2']`).

## Recipe states

`official_londonaq_classical_benchmark` has 9 plan recipes; tier A selects the
6 `role == core` recipes: mcar/mar/mnar × {0.1, 0.3}. The 3 `role == stress`
recipes (rate 0.5) are tier B.

## Diagnosis (confirmed)

The temporal CLI builds a partial `OfficialPreparationAutomationService` that
lacks the dataset service, preparation board, action service, guided-plan
service and artifact catalog. `_load_dataset_array` returns `None` before
`DatasetService.load_array()` can run, and every board-derived step then fails
with the cascade above. The LondonAQ `.dat` reader is not the cause.
