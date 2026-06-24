imputebench
├── 📁 admin
│   ├── 📁 audit
│   │   ├── ▶ benchmark-staleness
│   │   ├── ▶ junk [HIDDEN]
│   │   ├── ▶ mask-mechanism
│   │   ├── ▶ masking-truth
│   │   ├── ▶ raw-labels
│   │   ├── ▶ retire
│   │   ├── ▶ run
│   │   ├── ▶ runtime-timing
│   │   ├── ▶ saits-inference-truth
│   │   ├── ▶ size
│   │   ├── ▶ sqlite
│   │   ├── ▶ st-db
│   │   ├── ▶ training-history
│   │   └── ▶ ui-payload-baseline
│   ├── ▶ backup
│   ├── 📁 config [HIDDEN]
│   │   ├── ▶ set
│   │   ├── ▶ show
│   │   ├── 📁 style
│   │   │   ├── ▶ show
│   │   │   └── ▶ validate
│   │   └── ▶ validate
│   ├── ▶ db-sanitize
│   ├── ▶ env
│   ├── ▶ inject-doc-nav
│   ├── 📁 maintenance
│   │   ├── 📁 data
│   │   │   ├── ▶ execute
│   │   │   ├── ▶ plan
│   │   │   ├── ▶ reset
│   │   │   └── ▶ summary
│   │   ├── ▶ full-clean
│   │   ├── ▶ list-quarantine
│   │   ├── ▶ plan
│   │   ├── ▶ purge-quarantine
│   │   ├── ▶ purge-quarantined
│   │   ├── ▶ quarantine
│   │   ├── ▶ repair-metadata
│   │   ├── ▶ restore
│   │   ├── ▶ scan
│   │   └── ▶ sweep
│   ├── ▶ migrate
│   ├── 📁 paths
│   │   ├── ▶ inspect
│   │   ├── ▶ normalize
│   │   └── ▶ repair-registry
│   ├── ▶ prepare-official
│   ├── ▶ recover-training-history
│   ├── ▶ seed-london
│   └── ▶ status
├── 📁 algorithm [HIDDEN]
│   ├── ▶ delete
│   ├── ▶ list
│   ├── ▶ list-builtin
│   ├── ▶ register
│   ├── ▶ show
│   └── ▶ update
├── 📁 audit [HIDDEN]
│   ├── ▶ benchmark-staleness
│   ├── ▶ junk [HIDDEN]
│   ├── ▶ mask-mechanism
│   ├── ▶ masking-truth
│   ├── ▶ raw-labels
│   ├── ▶ retire
│   ├── ▶ run
│   ├── ▶ runtime-timing
│   ├── ▶ saits-inference-truth
│   ├── ▶ size
│   ├── ▶ sqlite
│   ├── ▶ training-history
│   └── ▶ ui-payload-baseline
├── 📁 calibration [HIDDEN]
│   ├── ▶ create
│   ├── ▶ create-default
│   ├── ▶ delete
│   ├── ▶ list
│   └── ▶ show
├── 📁 compare [HIDDEN]
│   ├── ▶ delete
│   ├── ▶ list
│   └── ▶ show
├── 📁 config [HIDDEN]
│   ├── ▶ set
│   ├── ▶ show
│   ├── 📁 style
│   │   ├── ▶ show
│   │   └── ▶ validate
│   └── ▶ validate
├── 📁 data
│   ├── 📁 calibration
│   │   ├── ▶ create
│   │   ├── ▶ create-default
│   │   ├── ▶ delete
│   │   ├── ▶ list
│   │   └── ▶ show
│   ├── 📁 dataset
│   │   ├── ▶ delete
│   │   ├── ▶ list
│   │   ├── ▶ register
│   │   ├── ▶ show
│   │   └── ▶ update
│   ├── 📁 ingest
│   │   ├── ▶ bundle
│   │   ├── ▶ dataset
│   │   ├── ▶ inspect
│   │   └── ▶ plugin
│   └── 📁 masking
│       ├── ▶ create
│       ├── ▶ delete
│       ├── ▶ list
│       ├── ▶ show
│       └── ▶ update
├── 📁 dataset [HIDDEN]
│   ├── ▶ delete
│   ├── ▶ list
│   ├── ▶ register
│   ├── ▶ show
│   └── ▶ update
├── 📁 env [HIDDEN]
│   └── ▶ show
├── 📁 evidence-gate [HIDDEN]
│   ├── ▶ comparison
│   ├── ▶ dashboard
│   └── ▶ storyboard
├── 📁 experiment
│   ├── 📁 run
│   │   ├── ▶ bulk-delete
│   │   ├── ▶ create
│   │   ├── ▶ delete
│   │   ├── ▶ execute
│   │   ├── ▶ execute-all
│   │   ├── ▶ list
│   │   ├── ▶ show
│   │   └── ▶ status
│   ├── 📁 recipe
│   │   ├── ▶ list
│   │   ├── ▶ show
│   │   ├── ▶ create
│   │   ├── ▶ clone
│   │   ├── ▶ update
│   │   ├── ▶ delete
│   │   ├── ▶ validate
│   │   ├── ▶ export
│   │   ├── ▶ history
│   │   ├── ▶ materialize
│   │   ├── 📁 entry
│   │   │   ├── ▶ list
│   │   │   ├── ▶ show
│   │   │   ├── ▶ add
│   │   │   ├── ▶ update
│   │   │   └── ▶ remove
│   │   └── 📁 algorithm
│   │       ├── ▶ add
│   │       └── ▶ remove
│   ├── 📁 st
│   │   ├── 📁 audit
│   │   │   └── ▶ st-db
│   │   ├── ▶ chapter06-readme
│   │   ├── ▶ compare
│   │   ├── ▶ env
│   │   ├── 📁 evidence
│   │   │   ├── ▶ audit
│   │   │   ├── ▶ export
│   │   │   ├── ▶ mirror
│   │   │   └── ▶ visual-audit
│   │   ├── 📁 experiment
│   │   │   ├── ▶ certify-official
│   │   │   ├── ▶ reset
│   │   │   └── ▶ run
│   │   ├── 📁 figures
│   │   │   └── ▶ generate
│   │   ├── ▶ gate
│   │   ├── 📁 graph
│   │   │   ├── ▶ build
│   │   │   ├── ▶ inspect
│   │   │   ├── ▶ list
│   │   │   └── 📁 sensitivity
│   │   │       └── ▶ report
│   │   ├── 📁 mask-bank
│   │   │   └── ▶ create
│   │   ├── 📁 plan
│   │   │   └── ▶ create
│   │   ├── ▶ preflight
│   │   ├── 📁 readiness
│   │   │   └── ▶ report
│   │   ├── 📁 recipe
│   │   │   ├── ▶ inspect
│   │   │   ├── ▶ list
│   │   │   ├── ▶ materialize
│   │   │   ├── ▶ plan
│   │   │   ├── ▶ validate
│   │   │   └── ▶ verify-materialization
│   │   ├── ▶ regenerate-chapter06
│   │   ├── 📁 run
│   │   │   └── ▶ execute
│   │   ├── 📁 scientific
│   │   │   └── 📁 gate
│   │   │       └── ▶ report
│   │   ├── 📁 tensor
│   │   │   └── ▶ inspect
│   │   └── 📁 training
│   │       ├── 📁 adequacy
│   │       │   ├── ▶ assess
│   │       │   └── ▶ report
│   │       ├── 📁 convergence
│   │       │   ├── ▶ assess
│   │       │   └── ▶ report
│   │       └── 📁 tier
│   │           ├── ▶ list
│   │           └── ▶ validate
│   └── 📁 temporal
│       ├── 📁 evidence
│       │   ├── ▶ export
│       │   └── ▶ inspect
│       ├── 📁 experiment
│       │   ├── ▶ certify
│       │   ├── ▶ dry-run
│       │   ├── ▶ materialize
│       │   ├── ▶ phase
│       │   ├── ▶ reset
│       │   ├── ▶ run
│       │   └── ▶ status
│       ├── 📁 gate
│       │   ├── ▶ list
│       │   ├── ▶ report
│       │   └── ▶ run
│       ├── 📁 lifecycle
│       │   ├── ▶ inspect
│       │   ├── ▶ policies
│       │   └── ▶ report
│       ├── 📁 preflight
│       │   ├── ▶ run
│       │   └── ▶ study
│       ├── 📁 prepare
│       │   ├── ▶ dry-run
│       │   ├── ▶ materialize
│       │   └── ▶ verify
│       ├── 📁 recipe
│       │   ├── ▶ algorithms
│       │   ├── ▶ inspect
│       │   ├── ▶ list
│       │   ├── ▶ masks
│       │   ├── ▶ tiers
│       │   └── ▶ validate
│       └── 📁 report
│           ├── ▶ readme
│           └── ▶ summary
├── 📁 ingest [HIDDEN]
│   ├── ▶ bundle
│   ├── ▶ dataset
│   ├── ▶ inspect
│   └── ▶ plugin
├── 📁 lab
│   ├── ▶ cleanup
│   ├── ▶ list
│   ├── ▶ show
│   ├── ▶ start
│   └── ▶ stop
├── 📁 locality [HIDDEN]
│   ├── ▶ inspect
│   └── ▶ normalize
├── 📁 maintenance [HIDDEN]
│   ├── 📁 data
│   │   ├── ▶ execute
│   │   ├── ▶ plan
│   │   ├── ▶ reset
│   │   └── ▶ summary
│   └── 📁 junk
│       ├── ▶ full-clean
│       ├── ▶ plan
│       ├── ▶ purge-quarantine
│       ├── ▶ purge-quarantined
│       ├── ▶ quarantine
│       ├── ▶ quarantine-list
│       ├── ▶ repair-metadata
│       ├── ▶ restore
│       ├── ▶ restore-quarantine
│       ├── ▶ scan
│       └── ▶ sweep
├── 📁 masking [HIDDEN]
│   ├── ▶ create
│   ├── ▶ delete
│   ├── ▶ list
│   ├── ▶ show
│   └── ▶ update
├── 📁 methods
│   ├── 📁 algorithm
│   │   ├── ▶ delete
│   │   ├── ▶ list
│   │   ├── ▶ list-builtin
│   │   ├── ▶ register
│   │   ├── ▶ show
│   │   └── ▶ update
│   └── 📁 plugin
│       ├── ▶ delete
│       ├── ▶ list
│       ├── ▶ register
│       ├── ▶ scaffold
│       ├── ▶ show
│       └── ▶ validate
├── 📁 plugin [HIDDEN]
│   ├── ▶ delete
│   ├── ▶ list
│   ├── ▶ register
│   ├── ▶ scaffold
│   ├── ▶ show
│   └── ▶ validate
├── 📁 repair [HIDDEN]
│   ├── ▶ junk [HIDDEN]
│   └── ▶ paths
├── 📁 result [HIDDEN]
│   ├── ▶ bulk-delete
│   ├── ▶ delete
│   ├── ▶ export-bundle
│   ├── ▶ export-spatial
│   ├── ▶ export-training-evidence
│   ├── ▶ list
│   ├── ▶ show
│   └── ▶ spatial-show
├── 📁 results
│   ├── 📁 compare
│   │   ├── ▶ delete
│   │   ├── ▶ list
│   │   ├── ▶ query
│   │   ├── ▶ results
│   │   ├── ▶ runs
│   │   ├── ▶ show
│   │   └── ▶ table
│   ├── 📁 evidence
│   │   ├── ▶ export
│   │   ├── ▶ export-batch
│   │   ├── ▶ list
│   │   └── 📁 preset
│   │       ├── ▶ clone
│   │       ├── ▶ create
│   │       ├── ▶ delete
│   │       ├── ▶ export
│   │       ├── ▶ list
│   │       ├── ▶ show
│   │       ├── ▶ update
│   │       └── ▶ validate
│   ├── 📁 gate
│   │   ├── ▶ comparison
│   │   ├── ▶ dashboard
│   │   └── ▶ storyboard
│   ├── ▶ inspect
│   ├── 📁 result
│   │   ├── ▶ bulk-delete
│   │   ├── ▶ delete
│   │   ├── ▶ export-bundle
│   │   ├── ▶ export-spatial
│   │   ├── ▶ export-training-evidence
│   │   ├── ▶ list
│   │   ├── ▶ show
│   │   └── ▶ spatial-show
│   └── 📁 view
│       ├── ▶ contact-sheet
│       ├── ▶ create
│       ├── ▶ create-comparison
│       ├── ▶ create-temporal
│       ├── ▶ export
│       ├── ▶ export-animation
│       ├── ▶ export-comparison
│       ├── ▶ export-frames
│       ├── ▶ show
│       ├── ▶ show-comparison
│       ├── ▶ summary
│       ├── ▶ timesteps
│       └── ▶ validate-comparison
├── 📁 run [HIDDEN]
│   ├── ▶ bulk-delete
│   ├── ▶ create
│   ├── ▶ delete
│   ├── ▶ execute
│   ├── ▶ execute-all
│   ├── ▶ list
│   ├── ▶ show
│   └── ▶ status
├── 📁 st [HIDDEN]
│   ├── 📁 audit
│   │   └── ▶ st-db
│   ├── ▶ chapter06-readme
│   ├── ▶ compare
│   ├── ▶ env
│   ├── 📁 evidence
│   │   ├── ▶ audit
│   │   ├── ▶ export
│   │   ├── ▶ mirror
│   │   └── ▶ visual-audit
│   ├── 📁 experiment
│   │   ├── ▶ certify-official
│   │   ├── ▶ reset
│   │   └── ▶ run
│   ├── 📁 figures
│   │   └── ▶ generate
│   ├── ▶ gate
│   ├── 📁 graph
│   │   ├── ▶ build
│   │   ├── ▶ inspect
│   │   ├── ▶ list
│   │   └── 📁 sensitivity
│   │       └── ▶ report
│   ├── 📁 mask-bank
│   │   └── ▶ create
│   ├── 📁 plan
│   │   └── ▶ create
│   ├── ▶ preflight
│   ├── 📁 readiness
│   │   └── ▶ report
│   ├── 📁 recipe
│   │   ├── ▶ inspect
│   │   ├── ▶ list
│   │   ├── ▶ materialize
│   │   ├── ▶ plan
│   │   ├── ▶ validate
│   │   └── ▶ verify-materialization
│   ├── ▶ regenerate-chapter06
│   ├── 📁 run
│   │   └── ▶ execute
│   ├── 📁 scientific
│   │   └── 📁 gate
│   │       └── ▶ report
│   ├── 📁 tensor
│   │   └── ▶ inspect
│   └── 📁 training
│       ├── 📁 adequacy
│       │   ├── ▶ assess
│       │   └── ▶ report
│       ├── 📁 convergence
│       │   ├── ▶ assess
│       │   └── ▶ report
│       └── 📁 tier
│           ├── ▶ list
│           └── ▶ validate
├── 📁 study
│   ├── 📁 dataset
│   │   └── ▶ profile
│   ├── 📁 missingness
│   │   └── ▶ profile
│   ├── 📁 results
│   │   └── ▶ summarize
│   └── 📁 temporal
│       ├── ▶ acf-pacf
│       ├── ▶ arma-order
│       └── ▶ stationarity
├── 📁 temporal [HIDDEN]
│   ├── 📁 evidence
│   │   ├── ▶ export
│   │   └── ▶ inspect
│   ├── 📁 experiment
│   │   ├── ▶ certify
│   │   ├── ▶ dry-run
│   │   ├── ▶ materialize
│   │   ├── ▶ phase
│   │   ├── ▶ reset
│   │   ├── ▶ run
│   │   └── ▶ status
│   ├── 📁 gate
│   │   ├── ▶ list
│   │   ├── ▶ report
│   │   └── ▶ run
│   ├── 📁 lifecycle
│   │   ├── ▶ inspect
│   │   ├── ▶ policies
│   │   └── ▶ report
│   ├── 📁 preflight
│   │   ├── ▶ run
│   │   └── ▶ study
│   ├── 📁 prepare
│   │   ├── ▶ dry-run
│   │   ├── ▶ materialize
│   │   └── ▶ verify
│   ├── 📁 recipe
│   │   ├── ▶ algorithms
│   │   ├── ▶ inspect
│   │   ├── ▶ list
│   │   ├── ▶ masks
│   │   ├── ▶ tiers
│   │   └── ▶ validate
│   └── 📁 report
│       ├── ▶ readme
│       └── ▶ summary
├── 📁 thesis
│   ├── ▶ algorithms
│   ├── ▶ all
│   ├── ▶ compare
│   ├── ▶ dataset
│   ├── ▶ gate
│   ├── ▶ gates
│   ├── ▶ missingness
│   ├── 📁 st
│   │   ├── 📁 figures
│   │   │   └── ▶ generate
│   │   ├── ▶ readme
│   │   └── ▶ regenerate
│   └── ▶ training
└── 📁 viewer [HIDDEN]
    ├── ▶ contact-sheet
    ├── ▶ create
    ├── ▶ create-comparison
    ├── ▶ create-temporal
    ├── ▶ export
    ├── ▶ export-animation
    ├── ▶ export-comparison
    ├── ▶ export-frames
    ├── ▶ show
    ├── ▶ show-comparison
    ├── ▶ summary
    ├── ▶ timesteps
    └── ▶ validate-comparison
