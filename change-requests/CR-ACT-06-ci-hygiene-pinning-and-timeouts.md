# CR-ACT-06: CI Hygiene (Pinned Dependencies, Unified Python, Job Timeouts)

| Field | Value |
|-------|-------|
| **CR** | CR-ACT-06 |
| **Title** | CI Hygiene (Pinned Dependencies, Unified Python, Job Timeouts) |
| **Status** | Proposed (this PR) |
| **Layer** | L3 (Actors Catalog) |
| **Owner** | Emmanuel A. Otchere |
| **Date** | 2026-10-28 |
| **Depends on** | lands with the CR-ACT-03..05 stack; content independent |
| **Companion to** | CR-CATALOG-STRUCT-11 (stakeholders slice, same defect class) |

## 1. Problem

CI hygiene defects shared with the sibling repos, none blocking but all cheap to fix while the repo is quiet:

- **Unpinned pip dependencies** in every workflow that installs anything (`catalog-conformance.yml`: `pip install pyyaml jsonschema pytest`; `validate-authorship.yml`: `pip install pytest`; `validate-entity-system.yml`: `pip install pyyaml`). A breaking upstream release turns CI red for reasons unrelated to any change.
- **Unpinned `dea-metaframework` checkout** in `catalog-conformance.yml` (CST-013/014): the CST suite floats on `main` of another repo.
- **`python-version` inconsistent** across workflows: `3.12` (validate-authorship, on `setup-python@v4`), `3.x` (validate-entity-system, on `setup-python@v5`), and none at all (catalog-conformance runs on the runner's default interpreter).
- **No `timeout-minutes` anywhere**: a hung job burns a full runner allocation (6h default).

## 2. Changes

| Workflow | Change |
|---|---|
| `catalog-conformance.yml` | `timeout-minutes: 15`; new `actions/setup-python@v5` with `python-version: "3.12"` (deterministic interpreter); `pip install pyyaml==6.0.3 jsonschema==4.26.0 pytest==9.1.1`; metaframework checkout pinned `ref: v2.6.0` |
| `validate-authorship.yml` | `timeout-minutes: 10`; `setup-python@v4` → `@v5` (unify with the rest of the org); `pip install pytest==9.1.1` |
| `validate-entity-system.yml` | `timeout-minutes: 10`; `python-version: "3.x"` → `"3.12"`; `pip install pyyaml==6.0.3` |
| `ci.yml` | `timeout-minutes: 10` (no pip/python of its own; the `dsanders11` action runs self-contained) |
| `validate-allocation.yml` | No change possible: the job is a reusable-workflow call, and `timeout-minutes` is not settable on a caller job; the timeout is owned by `dea-architecture-framework`'s `validate-against-model.yml` upstream |

The pinned versions are the ones the stakeholders repo's full local suite passed under on 2026-10-27 (CR-CATALOG-STRUCT-11); this repo's own suite (58 tests) passes under them locally, and the PR's runner pass proves them under 3.12. Actions SHA-pinning deliberately deferred (CR-DEA-BC-39 §8 precedent; needs an org-wide minor-version policy + SHA resolution).

## 3. Acceptance criteria

- AC-01 All five workflows parse as valid YAML.
- AC-02 `grep -Rn "pip install" .github/workflows/` shows only `==`-pinned installs.
- AC-03 `grep -Rn "python-version" .github/workflows/` shows `"3.12"` everywhere it appears.
- AC-04 `grep -Ln "timeout-minutes" .github/workflows/*.yml` returns only `validate-allocation.yml` (reusable call; owned upstream).
- AC-05 Local gates unchanged: `check_authorship --strict` exit 0, `pytest tests/ -q` 58 passed, regenerator `--check` exit 0.
