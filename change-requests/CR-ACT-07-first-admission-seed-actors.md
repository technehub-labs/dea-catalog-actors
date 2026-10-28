# CR-ACT-07: First Phase-2 Admission (Seed Actor Records, Actor Profile, Early-Exit Drop)

| Field | Value |
|-------|-------|
| **CR** | CR-ACT-07 |
| **Title** | First Phase-2 Admission (Seed Actor Records, Actor Profile, Early-Exit Drop) |
| **Status** | Proposed (this PR) |
| **Layer** | L3 (Actors Catalog) |
| **Owner** | Emmanuel A. Otchere |
| **Date** | 2026-10-28 |
| **Depends on** | CR-ACT-05 (org-wide id form; entity-system registry identity) |
| **Companion to** | none (first admission; proves the Phase-2 contract end-to-end) |

## 1. Purpose

The catalog has been a contract-only scaffold since Wave 3: schemas, id-form, conformance gates, and CI existed but no Phase-2 content admissions had ever landed, so the record contract was unproven. This CR lands the first admission and, in doing so, exercises the full documented workflow (entities/v1-alpha/README.md §Contributor Workflow, steps 1–7) for the first time.

## 2. Changes

### 2.1 Seed Actor records (3)

Three seed records covering three of the five `actor_type` values, at `entities/v1-alpha/<slug>-<hash6>/actors-actor-<slug>-<hash6>.yaml` per the flat layout and IDM-005:

| id | actor_type | scope |
|---|---|---|
| `actors:actor-architect-example-rwpe9z` | `human` | enterprise |
| `actors:actor-architecture-ci-pipeline-cz8zmp` | `system` | enterprise |
| `actors:actor-conformance-review-agent-svhhwu` | `ai-agent` | enterprise |

Each record carries the schema-required envelope (`id`, `type: Actor`, `name`, `actor_type`, `version`, `lifecycle_status`) plus CST-004's `lifecycle_status` and the registered `audit.author` authorship field. Hash suffixes are content-derived (first 4 SHA-256 bytes, base-31) at admission time, per `dea-metaframework/docs/id-system.md` §2.5. Each subtree carries a README.md (IDM-007).

Records use the flat `audit.author:` key shape: the authorship gate's `_extract_value` parses literal `key: value` lines, so a nested `audit:` block is invisible to it; the flat key keeps AUT-001..003 coverage real (verified: gate fires on a planted denylisted value).

### 2.2 Entity-system registry: Actor profile

`reconciliation/entity-system-registry.yaml` gains the `Actor` envelope profile (required fields: `id`, `type`, `name`, `version`, `lifecycle_status`, `actor_type`, `description`). Without it, ES-002 fires on any non-empty `type:` while `profiles: {}` — the gate was correct, the registry was incomplete.

### 2.3 Authorship registry: actor-record field

`reconciliation/authorship-fields.yaml` registers `audit.author` over `entities/v1-alpha/**/actors-*.yaml` (kind: yaml), per `docs/governance/cardinal-rules.md` R1, which notes registration as part of the first-admission CR.

### 2.4 Early-exit drop (`ci.yml`)

The Phase-2 empty-tree skip block is removed; `validate` now schema-validates every `entities/**/*.yaml|json` against `schemas/entity.schema.json` on each run. All three records validate.

### 2.5 Regenerator state-inference fix (bug found by this admission)

`infer_state` looked for the canonical file at `{entity_id}.yaml` — an id contains `:`, which can never be a literal filename in the flat layout. With records on disk this misclassified every canonical subtree as `state: candidate` in `CATALOG.yaml` while `read_canonical_yaml` (which normalizes `:` → `-`) correctly read the record — an internally inconsistent index. Fixed: the canonical filename is the id with `:` normalized to `-` (matching IDM-005 and the populated capabilities catalog's on-disk shape). `CATALOG.yaml` now emits `state: canonical` for all three. The same defect exists in the vendored `dea-catalog-stakeholders` regenerator (reported separately; it is latent there while that tree is empty).

## 3. Acceptance criteria (all verified locally)

- AC-01 `scripts/check_id_system.py --strict` → CONFORMANT (3 records, 0 findings; IDM-001..007 pass on real records for the first time).
- AC-02 `scripts/check_entity_system.py` → CONFORMANT (Actor profile resolves; ES-002 satisfied).
- AC-03 `scripts/check_authorship.py --strict` → CONFORMANT (audit.author coverage live).
- AC-04 All 3 records validate against `schemas/entity.schema.json`.
- AC-05 `scripts/regenerate_catalog.py --check` exit 0; `CATALOG.yaml` shows `entities: 3`, all `state: canonical`, `lifecycle_status: active` (index-schema enum).
- AC-06 `scripts/check_catalog_index.py --strict` → OK (3 entities).
- AC-07 CST suite (`dea-metaframework@v2.6.0`) → 16/16.
- AC-08 `pytest tests/ -q` → 58 passed.
- AC-09 Consumer validation against OpenDEAM v0.2.1 → exit 0.
- AC-10 `ci.yml` contains no early-exit block; schema validation runs unconditionally.
