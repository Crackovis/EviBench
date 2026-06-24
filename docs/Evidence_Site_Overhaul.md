# Evidence Site Overhaul

MOT 07 turns website-aware human evidence exports into a navigable static site.

For exports with `--experiment-id`, the experiment directory now contains:

- `index.html`: experiment landing page
- `dashboard.html`: lightweight dashboard shell
- `dashboard_data.json`: canonical dashboard payload from `HumanEvidencePack.dashboard_data()`
- `tables/*.html`: filterable/paginated table pages
- `figures/*.html` and `figures/storyboards/index.html`: figure and storyboard navigation
- `stats/index.html`: statistical-test status and pairwise-test links
- `provenance/index.html`: public provenance summary
- `evidence_dashboard.html`: compatibility monolith

The root site contains:

- `index.html`: rich evidence hub
- `site_hub_manifest.json`: hub metadata
- `assets/site.css`
- `assets/site.js`

The multipage site does not recompute metrics. It renders the existing human pack
projections and sidecars. Presentation HTML is excluded from the scientific
content fingerprint; data files such as `dashboard_data.json`, CSV, JSON, and
Markdown sidecars remain fingerprint inputs.

Offline behavior:

- The site uses only local CSS and JavaScript.
- `dashboard.html` attempts to load `dashboard_data.json`.
- If a browser blocks `file://` fetches, the page uses an embedded minimal JSON
  fallback and remains navigable.
- For full dashboard hydration, serve the site with a static server such as
  `python -m http.server --directory <site-root>`.
