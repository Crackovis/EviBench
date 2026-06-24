<!-- NAV:START -->
> [🏠 Home](../README.md) · 📍 **Recipe_Book_Schema_V1**

<details>
<summary>🗺️ Documentation Map</summary>

- [📂 README](../README.md)
- 📚 **docs/**
  - [🏗️ Architecture_Overview](Architecture_Overview.md)
  - [📝 Recipe_Book_Architecture](Recipe_Book_Architecture.md)
  - [📝 Recipe_Book_Schema_V1](Recipe_Book_Schema_V1.md) ← *you are here*
  - [💻 Recipe_Book_CLI_Reference](Recipe_Book_CLI_Reference.md)
  - [💻 Recipe_Book_Migration_Guide](Recipe_Book_Migration_Guide.md)
  - [📝 Recipe_Book_Architecture_Update_Report](Recipe_Book_Architecture_Update_Report.md)
  - [💻 CLI_Reference](CLI_Reference.md)
  - [📖 Introduction](Introduction.md)
  - [⚙️ Canonical_Workflow](Canonical_Workflow.md)
  - [💻 Temporal_Experiment_CLI_Reference](Temporal_Experiment_CLI_Reference.md)

</details>
<!-- NAV:END -->

# Recipe Book Schema v1

Schema identifier: `imputebench.recipe-book/v1`

JSON Schema Draft 2020-12 location:
`imputebench/resources/schemas/recipe_book_v1.schema.json`

---

## Top-Level Fields

| Field | Type | Required | Description |
|---|---|---|---|
| `schema` | string | yes | Must be `"imputebench.recipe-book/v1"` |
| `recipe_book` | object | yes | Book identity metadata (id, domain, kind, status, etc.) |
| `dataset` | object | yes | Dataset selector (id, version, path) |
| `defaults` | object | yes | Shared defaults for missingness, realizations, metrics |
| `algorithms` | array | yes | List of algorithm references |
| `generation` | object | no | Generation mode: explicit vs. matrix |
| `profiles` | object | no | Per-tier or per-group parameter overrides |
| `domain_config` | object | yes | Domain-specific configuration |
| `materialization` | object | no | Materialization policy |
| `metadata` | object | no | Arbitrary user metadata |

---

## `recipe_book` Contract

| Field | Type | Required | Description |
|---|---|---|---|
| `id` | string | yes | Unique book identifier. Regex: `^[a-z0-9_]+$`. Max 128 chars. |
| `domain` | string | yes | Experiment domain. Enum: `temporal`, `spatiotemporal` |
| `kind` | string | yes | Book kind. Enum: `dl`, `classical`, `baselines`, `spatiotemporal` |
| `status` | string | yes | Lifecycle status. Enum: `active`, `archived`, `deprecated`, `draft` |
| `claim_scope` | string | no | Ownership claim. Enum: `builtin`, `project`, `user`. Default: `user` |
| `tags` | array of string | no | Free-form tags. Max 10 tags, each max 64 chars |

### Domain and Kind Combinations

| Domain | Valid Kinds |
|---|---|
| `temporal` | `dl`, `classical`, `baselines` |
| `spatiotemporal` | `spatiotemporal` |

### Built-in Protection

When `claim_scope` is `builtin`, the book cannot be directly modified or deleted.
Clone it first to obtain a writable copy with `claim_scope: user`.

---

## Dataset Selector

| Field | Type | Required | Description |
|---|---|---|---|
| `dataset.id` | string | yes | Dataset identifier registered in the dataset registry |
| `dataset.version` | string | no | Pinned dataset version hash. If omitted, the latest version is used. |
| `dataset.path` | string | no | Override path. If omitted, the registered path is used. |

---

## Defaults

| Field | Type | Required | Description |
|---|---|---|---|
| `mask_families` | array of string | yes | Masking families to apply. Example: `["mcar", "mar", "mnar"]` |
| `rates` | array of number | yes | Missingness rates. Each value must be in `(0, 1)`. Example: `[0.1, 0.3, 0.5]` |
| `realizations` | integer | yes | Number of mask realizations per condition. Min 1, max 100. |
| `metrics` | array of string | yes | Evaluation metrics. Example: `["mae", "rmse"]` |
| `mask_params` | object | no | Additional masking parameters (e.g., block size for temporal_block) |

---

## Algorithm References

Each element in the `algorithms` array:

| Field | Type | Required | Description |
|---|---|---|---|
| `algorithm_id` | string | yes | Algorithm identifier registered in the algorithm registry |
| `config_overrides` | object | no | Algorithm-specific configuration overrides (JSON object) |
| `required_plugins` | array of string | no | Plugin slugs required by this algorithm |
| `enabled` | boolean | no | Whether this algorithm is active. Default: `true` |

---

## Generation Modes

The `generation` block controls how recipe entries are produced.

### Explicit Mode

```yaml
generation:
  mode: explicit
  entries:
    - algorithm_id: brits
      mask_family: mcar
      rate: 0.3
      realization: 1
```

When `mode` is `explicit`, every entry is listed manually. This is the default when
no `generation` block is present.

### Matrix Mode

```yaml
generation:
  mode: matrix
  constraints:
    max_entries: 5000
    skip_combinations: []
```

When `mode` is `matrix`, entries are generated from the Cartesian product of
algorithms, mask families, rates, and realizations (1 through `defaults.realizations`).
`skip_combinations` can list specific algorithm/mask/rate tuples to exclude.

---

## Profiles

Profiles override defaults for specific groups. A profile matches entries whose
keys match the profile's selector.

```yaml
profiles:
  dl_slow:
    selector:
      algorithm_id: ["saits", "saits_lc"]
    overrides:
      realizations: 5
  mnar_high:
    selector:
      mask_family: ["mnar"]
    overrides:
      rates: [0.3, 0.5, 0.7]
```

| Field | Type | Required | Description |
|---|---|---|---|
| `profiles.<name>.selector` | object | yes | Key-value pairs to match against entry attributes |
| `profiles.<name>.overrides` | object | yes | Values to override in matching entries |

---

## Domain Configurations

### Temporal (`domain: temporal`)

| Field | Type | Required | Description |
|---|---|---|---|
| `lookback` | integer | yes | Number of past timesteps used as input features |
| `horizon` | integer | yes | Forecast horizon in timesteps |
| `stride` | integer | no | Sliding window stride. Default: `1` |
| `normalize` | boolean | no | Apply per-feature normalization. Default: `true` |
| `train_split` | number | no | Fraction of data for training. Default: `0.7`. Must be in `(0, 1)` |

### Spatiotemporal (`domain: spatiotemporal`)

| Field | Type | Required | Description |
|---|---|---|---|
| `graph_policy` | string | yes | Graph construction policy. Enum: `correlation`, `distance`, `learned`, `hybrid` |
| `adjacency_config` | object | no | Policy-specific adjacency configuration |
| `nodes` | integer | yes | Number of spatial nodes (stations/sensors) |
| `features_per_node` | integer | yes | Number of features per node |
| `lookback` | integer | yes | Temporal lookback window |
| `spatial_masking_protocol` | string | no | Spatial missingness protocol. Default: `uniform` |

---

## Materialization Policy

| Field | Type | Required | Description |
|---|---|---|---|
| `auto_materialize` | boolean | no | Materialize on save. Default: `false` |
| `materialize_on_seed` | boolean | no | Materialize during seeding. Default: `true` for built-ins, `false` otherwise |
| `entry_cache_ttl` | string | no | TTL before re-materialization. ISO 8601 duration. Default: `"PT0S"` (always re-materialize) |

---

## Safe YAML Rules

The YAML parser enforces the following safety constraints:

1. **No custom tags.** Any `!Tag` directive causes a parse error.
2. **No duplicate keys.** Duplicate mapping keys at any nesting level are rejected.
3. **String size limit.** Individual string values must not exceed 64 KiB.
4. **Document size limit.** The total YAML document must not exceed 1 MiB before
   matrix expansion.
5. **No anchors or aliases.** YAML anchors (`&`) and aliases (`*`) are rejected to
   prevent reference cycles and accidental sharing.

Violations produce a validation error with the exact location (line, column, field
path) of the problem.

---

## Example YAML

```yaml
schema: imputebench.recipe-book/v1

recipe_book:
  id: my_temporal_dl_benchmark
  domain: temporal
  kind: dl
  status: draft
  claim_scope: user
  tags: ["londonaq", "dl", "benchmark"]

dataset:
  id: london_aq
  version: "fa9c7108"

defaults:
  mask_families: ["mcar", "mar", "mnar"]
  rates: [0.1, 0.3, 0.5]
  realizations: 3
  metrics: ["mae", "rmse"]

algorithms:
  - algorithm_id: brits
    enabled: true
  - algorithm_id: saits
    enabled: true
  - algorithm_id: gru_d
    config_overrides:
      epochs: 200
    enabled: true

generation:
  mode: matrix

domain_config:
  lookback: 24
  horizon: 1
  stride: 1
  normalize: true
  train_split: 0.7

materialization:
  auto_materialize: false

metadata:
  author: researcher
  description: "Temporal DL benchmark on LondonAQ with three missingness mechanisms"
```

<!-- FOOTER:START -->

---

> [← Recipe_Book_Architecture](Recipe_Book_Architecture.md) · [⬆ Top](#) · [🏠 Home](../README.md) · [Recipe_Book_CLI_Reference →](Recipe_Book_CLI_Reference.md)
<!-- FOOTER:END -->
