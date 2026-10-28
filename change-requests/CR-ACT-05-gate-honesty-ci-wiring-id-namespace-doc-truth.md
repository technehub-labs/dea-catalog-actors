# CR-ACT-05: Gate Honesty, CI Test Wiring, Id-Namespace Alignment, Doc Truth

| Field | Value |
|-------|-------|
| **CR** | CR-ACT-05 |
| **Title** | Gate Honesty, CI Test Wiring, Id-Namespace Alignment, Doc Truth |
| **Status** | Proposed (this PR) |
| **Layer** | L3 (Actors Catalog) |
| **Owner** | Emmanuel A. Otchere |
| **Date** | 2026-10-28 |
| **Depends on** | CR-ACT-02 (entity-system registry), CR-ACT-03 (index arithmetic) |
| **Companion to** | CR-CATALOG-STRUCT-10 (stakeholders slice, same defect class) |

## 1. Problem (verified on `main` @ `3c1eb45`)

Four truth deficits in the layer that proves this catalog's trust, all cheap to fix while the catalog is still empty:

1. **`validate-entity-system.yml` runs its strict gate with `continue-on-error: true`** — the step can never block a PR, while CR-ACT-02 and the CHANGELOG advertise `--strict` on every PR. Vacuous today (0 entities) but structurally dishonest the day the first record lands.
2. **The test suite runs nowhere.** `catalog-conformance.yml` installs pytest and never runs a test; `tests/test_check_entity_system.py` (32 passing tests) executes in no workflow; only the authorship module runs, in a different workflow.
3. **The repo's two id gates contradict each other** about the canonical id form: `scripts/check_id_system.py:44` IDM-001 requires the org-wide form `^actors:actor-[a-z0-9-]+-[a-z2-9]{6}$` (namespace `actors:`, content hash), while `reconciliation/entity-system-registry.yaml` (CR-ACT-02) declares `canonical_prefix: "dea:"` / `canonical_pattern: "^dea:actor-[a-z0-9-]+$"` (no hash). No future record can satisfy both. The registry, not the IDM gate, is the outlier (this repo's own Wave-3 IDM bring-up, PR #3, already adopted the org-wide form).
4. **Documentation copied from siblings without adaptation**: CONTRIBUTING titled for the wrong repo (`dea-catalog-processes`) with a phantom "32 conformance gates" claim; a processes-repo glob in the cardinal rules (`entities/v1-alpha/**/processes-*.yaml`); an entities README documenting gates and artifacts that do not exist here (register-audit, CR-metadata, allocation-matrix gates; inventory/baseline files); a README whose entity table says Layer L1 while the OpenDEAM v0.2.1 root model allocates `dea:entity-actor` to L3 / `L3-work-organization` (verified with `validate_consumer.py`), a stale `v3.0.0-alpha` metamodel claim, `?entity=Actor` viewer badge (canonical is `?entity=AC`), and a structure tree that omits `CATALOG.yaml`, `reconciliation/`, `scripts/`, and four of the five workflows.

## 2. Changes (ported from CR-CATALOG-STRUCT-10, adapted to actors)

1. Delete `continue-on-error: true` from the entity-system strict step. Verified safe: the gate exits 0 on this tree (vacuously CONFORMANT); the `--self-test` step is unaffected.
2. `catalog-conformance.yml` gains `python -m pytest tests/ -q` after the gates (retiring the install-but-never-run pytest step).
3. `reconciliation/entity-system-registry.yaml` `identity:` block flips to the org-wide form IDM-001 already enforces: `canonical_prefix: "actors:"`, `canonical_pattern: "^actors:actor-[a-z0-9-]+-[a-z2-9]{6}$"`, `legacy_prefix: "dea:"`, `legacy_pattern: "^dea:actor-[a-z0-9-]+$"`.
4. Doc truth sweep: CONTRIBUTING retitled for `dea-catalog-actors` with real workflow names; cardinal-rules glob corrected to `actors-*.yaml` and enforcement wiring corrected (no "gate [32]" rollup claim); entities README rewritten to list only the gates/artifacts that exist here, with the Phase-2 not-yet-adopted set called out, and pointing to each sibling's `CATALOG.yaml` instead of hardcoded counts; README aligned to the pointer and the root model (L3, `L3-work-organization`, v0.2.1 / OpenDEAM v0.2.1, `?entity=AC` badge, actual structure tree).

## 3. Acceptance criteria

- AC-01 `grep -n "continue-on-error" .github/workflows/validate-entity-system.yml` returns nothing.
- AC-02 `catalog-conformance.yml` contains a `Run test suite` step running `python -m pytest tests/ -q`.
- AC-03 `python3 scripts/check_entity_system.py` exits 0 with the flipped registry identity (vacuously CONFORMANT, 0 entities).
- AC-04 `python3 scripts/check_id_system.py --strict` exits 0; the registry pattern and IDM-001 pattern now agree.
- AC-05 No `processes-*.yaml` glob, no `dea-catalog-processes` title, no "gate [32]" claim, no `v3.0.0-alpha`, no `?entity=Actor` badge remains in CONTRIBUTING.md, docs/governance/cardinal-rules.md, entities/v1-alpha/README.md, README.md.
