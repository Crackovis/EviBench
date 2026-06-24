# Inventaire Bulk Action — Commandes CLI EviBench

**Date:** 2026-06-13  
**Objectif:** Identifier toutes les commandes CLI qui gagneraient à recevoir des capacités d'action en masse (bulk).

---

## 1. Infrastructure Bulk Existante

Le codebase possède déjà un pattern bulk mature :

### 1.1 Cycle Scan → Plan → Execute (avec token de confirmation)

```
imputebench maintenance junk scan       → rapport JSON
imputebench maintenance junk plan        → preview + confirmation_token
imputebench maintenance junk execute     → --confirm-token <tok>
```

**Forces du pattern :**
- Dry-run par défaut, `--execute` explicite requis
- Token de confirmation déterministe (hash SHA256 du plan)
- Impact preview (items, bytes, catégories)
- Partial success/failure reporting
- Audit log
- Restore possible depuis la quarantaine

### 1.2 Preset-based Bulk Operations

```
imputebench maintenance data plan --preset delete-failed-empty-results
imputebench maintenance data reset --profile reset-generated-outputs
imputebench maintenance junk sweep --profile safe
imputebench maintenance junk full-clean   (4 phases coordonnées)
```

### 1.3 Services Bulk Déjà Disponibles

| Service | Méthodes Bulk |
|---|---|
| `AdminDataManagementService` | `build_preset_plan()`, `execute_plan()`, `build_reset_plan()` |
| `JunkScanner` | `scan_all()`, `scan_filesystem()`, `scan_metadata()` |
| `JunkCleanupPlanner` | `build_plan(report, action, categories, dry_run)` |
| `JunkCleanupExecutor` | `execute(plan, confirm_token)` |
| `QuarantineInventoryService` | `list_items()`, `preview_purge()`, `purge_old()`, `restore()` |
| `MaintenanceCommandCenterService` | `preview_preset()`, `execute_preset()` |

---

## 2. Commandes Classifiées par Priorité Bulk

### 2.1 🔴 PRIORITÉ 1 — Impact Élevé, Effort Modéré

Ces commandes sont les candidates immédiates. Le pattern scan→plan→execute existe déjà, il suffit de l'appliquer.

#### `run delete` → `run bulk-delete`

| Aspect | État actuel | Cible bulk |
|---|---|---|
| Accepte | Un seul `RUN_ID` | `--status failed`, `--dataset-id`, `--algorithm-id`, `--older-than-days`, `--all` |
| Dry-run | ✅ Oui | ✅ Conserver |
| Impact preview | ✅ Oui | ✅ Étendre à N runs |
| Confirmation | ✅ `--force` | ✅ Ajouter `--confirm-token` |
| Cascade | `--cascade/--no-cascade` | Même sémantique × N |
| Service gap | `RunService.delete()` single | Besoin de `inspect_bulk_delete_impact(filters)` |

**Use case concret :**
```bash
# Supprimer tous les runs en échec
imputebench run bulk-delete --status failed --dry-run
imputebench run bulk-delete --status failed --execute --confirm-token abc123

# Supprimer les runs de plus de 30 jours
imputebench run bulk-delete --older-than-days 30 --dry-run
```

---

#### `result delete` → `result bulk-delete`

| Aspect | État actuel | Cible bulk |
|---|---|---|
| Accepte | Un seul `RESULT_ID` | `--run-id`, `--status`, `--algorithm-id`, `--older-than-days` |
| Service gap | `ResultService.delete()` single | Besoin de `delete_by_filter()` |

**Use case concret :**
```bash
# Nettoyer les résultats d'un run
imputebench result bulk-delete --run-id <UUID> --dry-run
imputebench result bulk-delete --run-id <UUID> --execute --confirm-token abc123
```

---

#### `plugin delete` → bulk multi-slug

| Aspect | État actuel | Cible bulk |
|---|---|---|
| Accepte | Un seul `SLUG` | `SLUG [SLUG...]` (multi-argument), `--family`, `--all-non-builtin` |

**Use case concret :**
```bash
# Quarantiner plusieurs plugins d'un coup
imputebench plugin delete arma brits --dry-run
imputebench plugin delete arma brits --execute --force
```

---

### 2.2 🟡 PRIORITÉ 2 — Batch Operations

#### `run execute` → batch execution

| Aspect | État actuel | Cible bulk |
|---|---|---|
| Lance | 1 run à la fois | `--filter status=planned`, `--all-planned`, `--max-parallel N` |

**Use case concret :**
```bash
# Exécuter tous les runs planifiés
imputebench run execute-all --status planned --dry-run
imputebench run execute-all --status planned --max-parallel 4
```

**⚠️ Risque :** L'exécution parallèle nécessite une gestion sérieuse des workers, du scheduling, et de la progression. Implémenter seulement si `ExperimentRunner` supporte le parallélisme.

---

#### `dataset/algorithm/masking delete` → `--all-unused`

| Aspect | État actuel | Cible bulk |
|---|---|---|
| dataset delete | UUID unique | `--all-unreferenced` (pas de runs liés) |
| algorithm delete | UUID unique | `--all-unreferenced` |
| masking delete | UUID unique | `--all-unreferenced` |

---

#### `compare delete` → `--all-archived`, `--all-stale`

| Aspect | État actuel | Cible bulk |
|---|---|---|
| compare delete | COMPARISON_ID unique | `--all-archived`, `--all-stale` |

---

### 2.3 🟢 PRIORITÉ 3 — Nice-to-Have

#### `temporal experiment reset` → multi-tier

Actuellement : `--tier smoke` (un seul tier). Pourrait accepter `--tier all` pour resetter smoke + a + b.

#### `maintenance junk restore` → déjà bulk !

Accepte déjà `--item-id` multiple (repeatable). ✅

---

## 3. Commandes Déjà Bulk (Aucune Action Requise)

| Commande | Nature bulk |
|---|---|
| `maintenance junk scan` | Scan tout le workspace |
| `maintenance junk plan` | Planifie sur tout le rapport |
| `maintenance junk quarantine` | Exécute sur tout le plan |
| `maintenance junk purge-quarantined` | Bulk par âge |
| `maintenance junk restore` | Multi `--item-id` |
| `maintenance junk sweep` | Profil bulk |
| `maintenance junk full-clean` | 4 phases coordonnées |
| `maintenance data plan` | Preset bulk |
| `maintenance data reset` | Profil bulk |
| `admin db-sanitize` | Multi-pass bulk |
| `admin prepare-official` | `--prepare-all-safe` = batch |
| `result export-training-evidence` | `--run <id>` = batch |

---

## 4. Pattern Bulk Réutilisable

Le pattern éprouvé du `maintenance junk` peut être répliqué :

```
1. SCAN     → Collecter les items candidats (filtres CLI → query service)
2. PREVIEW  → Afficher l'impact (N items, résumé, blockers)
3. TOKEN    → Générer un confirmation_token déterministe
4. CONFIRM  → Exiger --execute --confirm-token <tok>
5. EXECUTE  → Appliquer, reporter succès/échecs partiels
6. AUDIT    → Écrire un audit log
```

### Contrat d'interface pour nouvelles commandes bulk :

```python
@command("bulk-delete")
@click.option("--status", "status_filter")
@click.option("--dataset-id")
@click.option("--older-than-days", type=int)
@click.option("--dry-run/--no-dry-run", default=True)
@click.option("--execute", is_flag=True)
@click.option("--confirm-token", default="")
@click.option("--format", type=click.Choice(["table", "json"]), default="table")
def bulk_delete(status_filter, dataset_id, older_than_days, dry_run, execute, confirm_token, format):
    """Bulk delete runs matching filters."""
    # 1. Collecter
    items = service.collect_matching(filters)
    # 2. Preview
    impact = build_impact_summary(items)
    # 3. Token
    token = generate_confirmation_token(impact)
    # 4. Gate
    if not execute or not confirm_token:
        display_impact(impact, token)
        return
    if confirm_token != token:
        raise click.ClickException("Token mismatch")
    # 5. Execute
    results = service.bulk_execute(items)
    # 6. Report
    display_results(results)
```

---

## 5. Implémentation — État

### Phase 1 — Bulk Delete (P0) ✅ IMPLÉMENTÉ

| # | Commande | Filtres | Statut |
|---|---|---|---|
| 1 | `run bulk-delete` | `--status`, `--dataset-id`, `--algorithm-id`, `--older-than-days` | ✅ |
| 2 | `result bulk-delete` | `--run-id`, `--status`, `--algorithm-id`, `--older-than-days` | ✅ |
| 3 | `plugin delete SLUG [SLUG...]` | Multi-argument | ✅ |

### Phase 2 — Batch Operations (P1) ✅ IMPLÉMENTÉ

| # | Commande | Options | Statut |
|---|---|---|---|
| 4 | `run execute-all` | `--status`, `--max-parallel`, `--dry-run/--execute` | ✅ |
| 5 | `compare delete --all-archived` | *(P1 deferred)* | ⏳ |

### Phase 3 — Generalisation (P2) ⏳

| # | Commande | Statut |
|---|---|---|
| 6 | `dataset/algorithm/masking delete --all-unreferenced` | ⏳ |
| 7 | `temporal experiment reset --tier all` | ⏳ |

---

## 6. Services Créés

| Service | Fichier | Rôle |
|---|---|---|
| `RunBulkService` | `services/run_bulk_service.py` | `collect_matching()`, `build_bulk_delete_impact()`, `bulk_delete()`, `build_bulk_execute_impact()` |
| *(PluginLifecycleService)* | Déjà existant | `delete_plugin()` utilisé en boucle pour multi-slug |

### 6.1 Tests

| Fichier | Tests | Statut |
|---|---|---|
| `tests/cli/test_run_bulk.py` | 13 | ✅ |
| `tests/cli/test_result_bulk.py` | 5 | ✅ |
| `tests/cli/test_plugin_crud.py` | 13 (dont 2 multi-slug) | ✅ |

---

*Fin de l'inventaire.*
