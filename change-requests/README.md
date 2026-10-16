# Change Requests

This directory tracks formal change requests (CRs) for `technehub-labs/dea-catalog-actors`.

| CR | Title | Status | Summary |
|---|---|---|---|
| [CR-ACT-02](CR-ACT-02-entity-system-actors.md) | Entity System Stage 5-6 (Actors Slice) | **Proposed (this PR)** | Actors slice of the General Entity System institutionalisation (cross-repo cascade T7). New `reconciliation/entity-system-registry.yaml` (per-repo; generic; no profiles; canonical pattern matches the repo namespace). New `scripts/check_entity_system.py` (5-rule umbrella gate; copied verbatim from `dea-catalog-processes`). New `tests/test_check_entity_system.py` (32 tests; 4 pilot-specific tests made generic; all pass). New `.github/workflows/validate-entity-system.yml` (CI). Live: gate vacuously CONFORMANT. D-004 clean. 118a/b/c authorship gates remain CONFORMANT. Follow-on: T8 legacy ID reconciliation. |
