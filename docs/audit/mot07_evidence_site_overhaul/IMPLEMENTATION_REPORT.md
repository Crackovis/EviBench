# MOT 07 Evidence Site Overhaul Implementation Report

HEAD baseline:

```text
b298b91daa3c8842fae0548362a9243466951d88
```

Implemented:

- Added page read models in `imputebench/read_models/human_evidence_site_pages.py`.
- Added shared static assets in `site_assets.py`.
- Added shared page shell in `site_shell.py`.
- Added experiment pages in `site_pages.py`.
- Added table pages in `table_pages.py`.
- Added figure/storyboard pages in `figure_gallery.py`.
- Added orchestration service in `site_page_service.py`.
- Updated website-aware export mode so `index.html` is the landing page,
  `dashboard.html` is the split dashboard, and `evidence_dashboard.html` remains
  the monolithic fallback.
- Updated the root hub to use shared assets and richer experiment cards.
- Updated metadata defaults so `dashboard_path` points to `dashboard.html`.
- Excluded presentation HTML from content fingerprinting while keeping
  `dashboard_data.json` and sidecars as fingerprinted content.

Generated experiment inventory:

```text
index.html
dashboard.html
dashboard_data.json
evidence_dashboard.html
tables/index.html
tables/primary_comparison.html
tables/pollutant_breakdown.html
tables/per_recipe.html
tables/framework/index.html
figures/index.html
figures/ranking.html
figures/framework.html
figures/storyboards/index.html
stats/index.html
provenance/index.html
```

Generated root inventory:

```text
index.html
site_hub_manifest.json
assets/site.css
assets/site.js
```

Public safety:

- Shared shell rejects visible UUIDs and absolute local paths in generated HTML.
- Hub renderer keeps UUID leak protection.
- No inline `onclick`, `onchange`, or `onload` handlers are generated.
- Shared JS uses local DOM filtering/pagination and does not use `eval` or
  `innerHTML`.

Tests run:

```text
pytest tests\human_evidence\site tests\human_evidence\site_overhaul -q
pytest tests\human_evidence -q
pytest tests\results_interaction -q
pytest -q
```

Result:

```text
40 passed
288 passed
368 passed, 81 warnings
pytest -q: collection failed on unrelated missing modules
```

Global collection blockers observed:

```text
tests.cli
plugins.stgcn
scripts.audit_algorithm_design_inventory
imputebench.services.algorithm_conformance_service
imputebench.services.algorithm_card_service
imputebench.services.algorithm_execution_truth_service
```

Known limitations:

- Storyboard thumbnails use the existing PNGs scaled by CSS; no thumbnail
  derivative files are generated in v1.
- Experiment switcher data is currently omitted from experiment pages unless a
  future pass injects hub manifest context before rendering each experiment.
