# BASELINE — MOT `illustration` CLI

Captured before implementation (ILLUSTRATION-00).

## CLI root domains (canonical layout, before)

7 canonical domains registered in `_register_canonical_layout()`:

```
data  methods  experiment  results  study  admin  lab
```

plus hidden deprecated legacy aliases (dataset, algorithm, plugin, run, result,
st, temporal, …) and the deprecated `thesis` facade.

`_KNOWN_ROOT_COMMANDS` did **not** contain `illustration`.

## Dependencies (pyproject.toml, before)

Core already includes the libraries needed for figure rendering:

```
click, numpy, pandas, tabulate, matplotlib (>=3.8,<3.10), streamlit,
scikit-learn, PyYAML, py7zr, jsonschema
```

No `graphviz`, no `networkx`. Optional extras: `dev`, `dl`, `data-io`,
`evidence-stats`. The MOT adds **no new mandatory dependency**.

Installed at audit time: Python 3.10.15, matplotlib 3.10.8, numpy 2.2.6.

## Runtime classifier (before)

`imputebench/cli/runtime_command_classifier.py` classified `data/methods/admin/
audit/repair/validate` and `results export-human`/`result`/`compare` as
`GPU_NOT_APPLICABLE`. There was **no** `illustration` classification.

## Evaluations v0.3 figure directory status (before)

```
…/Revisions/Thesis/v0.3/
├── README.md
├── docs/
└── rendering/
```

No `_/generated/figures/` staging directory existed yet. `rendering/7-figures/`
is the shared image directory; nothing enters `rendering/` without a full spec.

## Chapter 2 structure (target)

Chapter 2 is the first active writing target, with five literature-review
sections. Captions must follow `Figure 2.x: …` style. Four conceptual figures
are required:

```
Figure 2.1 — MCAR / MAR / MNAR              (§2.1)
Figure 2.2 — Imputation taxonomy            (§2.2–2.3)
Figure 2.3 — Sensor graph & ST flow         (§2.4)
Figure 2.4 — Research gaps & positioning    (§2.5)
```

## Reusable services available (for evidence-aware plots)

- `comparison_global.json` (schema `imputebench.human-comparison-table/v1`) with
  `algorithms`, `metric_name`, `direction`, and per-cohort `rows[].cells[]`
  carrying `{algorithm_id, mean, std, count, is_best, comparable}`.
- `HumanRankingFigureService` establishes the project methodology: aggregate the
  within-recipe **rank**, never average raw RMSE across recipes. The new
  `plot ranking` follows the same rule.

## No existing illustration group

Confirmed: no `imputebench/cli/illustration/` and no `imputebench/illustration/`
package existed before this MOT.
