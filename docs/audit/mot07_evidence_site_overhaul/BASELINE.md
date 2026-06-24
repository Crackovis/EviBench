# MOT 07 Evidence Site Overhaul Baseline

HEAD at implementation start:

```text
b298b91daa3c8842fae0548362a9243466951d88
```

Baseline observations:

- Existing site mode from MOT 05 published an experiment directory under
  `OUTPUT_DIR/<experiment_id>`.
- `expN/index.html` was an alias of the monolithic `evidence_dashboard.html`.
- The root hub existed, but used inline CSS and exposed a small metadata card.
- No shared `assets/site.css` or `assets/site.js` existed.
- No split `dashboard.html` plus `dashboard_data.json` renderer existed.
- Table, figure, storyboard, stats, and provenance HTML pages were not generated.
- The monolithic `evidence_dashboard.html` already remained available and is
  preserved as a compatibility fallback.

Concurrent worktree note:

MOT 06 parallel/tier-merge changes were present during this implementation in
unrelated files. MOT 07 changes are scoped to the evidence site rendering and the
minimal export-service hook required to call it.
